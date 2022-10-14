<script lang="ts">
  import { listen } from "svelte/internal";

  export let randomDate: Date;

  const days = [
    "Sunday",
    "Monday",
    "Tuesday",
    "Wednesday",
    "Thursday",
    "Friday",
    "Saturday",
  ];

  const regJANFEBDoomsdays: Record<number, number> = {
    1: 3,
    2: 28,
  };

  const leapJANFEBDoomsdays: Record<number, number> = {
    1: 4,
    2: 29,
  };

  const regDoomsdays: Record<number, number> = {
    3: 14,
    4: 4,
    5: 9,
    6: 6,
    7: 11,
    8: 8,
    9: 5,
    10: 10,
    11: 7,
    12: 12,
  };

  const centuryAnchorIndecies = ["Tuesday", "Sunday", "Friday", "Wednesday"];
  let yearC: number = Number.parseInt(
    randomDate.getFullYear().toString().slice(0, 2)
  );
  let yearD: number = Number.parseInt(
    randomDate.getFullYear().toString().slice(2)
  );
  function isleapyear() {
    return (
      (year % 4 == 0 && year % 100 == 0 && year % 400 == 0) ||
      (year % 4 == 0 && year % 100 > 0)
    );
  }
  function getFinalDoomsdayDistance() {
    let finalYearD = yearD % 2 == 0 ? yearD : yearD + 11;
    finalYearD /= 2;
    finalYearD = finalYearD % 2 == 0 ? finalYearD : finalYearD + 11;
    finalYearD %= 7;
    finalYearD = 7 - finalYearD;
    let finalAnchorDay = (finalYearD + finalYearC) % 7;
    let finalDoomsdayDistance = (day - doomsday) % 7;
    return finalDoomsdayDistance + finalAnchorDay;
  }
  let runningYearD = yearD;
  let finalYearC = days.findIndex((v) => v == centuryAnchorIndecies[yearC % 4]);
  let anchorDay: number;
  let year = randomDate.getFullYear();
  let day = randomDate.getDate();
  let month = randomDate.getMonth() + 1;
  let doomsday: number =
    month > 2
      ? regDoomsdays[month]
      : isleapyear()
      ? leapJANFEBDoomsdays[month]
      : regJANFEBDoomsdays[month];
  let doomsdayDistance: number;
  let finalDoomsdayDistance: number = getFinalDoomsdayDistance();
  let normalisedDoomsdayDistance: number = finalDoomsdayDistance<0?7+finalDoomsdayDistance:finalDoomsdayDistance;
</script>

<div>
  <h1 class="flex flex-row justify-center">
    {days[randomDate.getDay()]}
  </h1>
  <br />
  <h1>Solution Steps</h1>
  <h3>Step 1:</h3>
  <span>Find the anchor day for century {yearC}00</span>
  <ol>
    <li>{yearC}mod4 = {yearC % 4}</li>
    <li>Assign to Tuesday, Sunday, Friday, Wednesday</li>
    <li>
      Therefore anchor day for century {yearC}00 = {centuryAnchorIndecies[
        yearC % 4
      ]} = {finalYearC}
    </li>
  </ol>
  <h3>Step 2:</h3>
  <span>Find the anchor day for {year}</span>
  <ol>
    <li>Take last digits {yearD}</li>
    <li>
      {yearD % 2 == 0
        ? `Since ${yearD} is even, do nothing`
        : `Since ${yearD} is odd, add 11; therefore ${(runningYearD += 11)}`}
    </li>
    <li>Divide by 2; {(runningYearD /= 2)}</li>
    <li>
      {runningYearD % 2 == 0
        ? `Since ${runningYearD} is even, do nothing`
        : `Since ${runningYearD} is odd, add 11; therefore ${(runningYearD += 11)}`}
    </li>
    <li>{runningYearD}mod7 = {(runningYearD %= 7)}</li>
    <li>7-{runningYearD}={(runningYearD = 7 - runningYearD)}</li>
    <li>
      Count forwards {runningYearD} days from century anchor {finalYearC}; ({runningYearD}+{finalYearC})mod7={(anchorDay =
        (runningYearD + finalYearC) % 7)}
    </li>
    <li>
      Therefore anchor day for {yearC}{yearD} is {anchorDay}
      = {days[anchorDay]}
    </li>
  </ol>
  <h3>Step 3:</h3>
  <span>Find doomsday in month {month}</span>
  <ol>
    <li>
      {month <= 2
        ? `Since ${month}<=2, check if leapyear`
        : `Since ${month}>2, ignore leap years`}
    </li>
    {#if month <= 2}
      <li>
        Since {yearD}mod4={year % 4}; {year % 4 > 0
          ? "Not a leap year"
          : "Could be leapyear"}
      </li>
      {#if year % 4 == 0}<li>
          Since {year}mod100={year % 100}; {year % 100 > 0
            ? "Is a leapyear"
            : "Could be a leapyear"}
        </li>{/if}
      {#if year % 100 == 0}
        Since {year / 100}mod4={(year / 100) % 4}; {year % 400 > 0
          ? "Not a leapyear"
          : "Is a leapyear"}
      {/if}
    {/if}
    <li>
      Doomsday in month {month} is: {doomsday}
    </li>
  </ol>
  <h3>Step 4:</h3>
  <span>Calculate day of the week</span>
  <ol>
    <li>
      Find distance to doomsday; {day}-{doomsday} = {(doomsdayDistance =
        day - doomsday)}
    </li>
    <li>Find {doomsdayDistance}mod7 = {(doomsdayDistance %= 7)}</li>
    <li>
      Add to anchor day; {doomsdayDistance}+{anchorDay} = {(doomsdayDistance +=
        anchorDay)}
    </li>
    {#if finalDoomsdayDistance < 0}
      <li>{finalDoomsdayDistance} is equal to +{7+finalDoomsdayDistance}</li>
    {/if}
    <li>
      Therefore the weekday is {normalisedDoomsdayDistance}mod7 = {normalisedDoomsdayDistance% 7} =
      {days[normalisedDoomsdayDistance % 7]}
    </li>
  </ol>
</div>
