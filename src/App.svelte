<script>
  import Summary from "./Summary.svelte";
  import CaseData from "./CaseData.svelte";
  import Rzero from "./Rzero.svelte";
  import Spinner from "./Spinner.svelte";
  import TableView from "./TableView.svelte";
  import Notice from "./Notice.svelte";
  import LogGraphs from "./LogGraphs.svelte";
  import { onMount } from "svelte";

  import TopAppBar, {
    Row,
    Section,
    Title,
    FixedAdjust,
    DenseFixedAdjust,
    ProminentFixedAdjust,
    ShortFixedAdjust,
  } from "@smui/top-app-bar";

  import { fade } from "svelte/transition/";

  let dateAxis,
    totalCases,
    recoveries,
    deaths,
    activeCases,
    totalCasesDelta,
    totalCasesR0,
    recoveriesDelta,
    deathsDelta,
    activeCasesDelta,
    summaryData,
    lastUpdate,
    r0avg,
    err = null;

  const round = (num, decimals = 3) => {
    return 1 * num.toFixed(decimals);
  };

  const calculateDeltas = (input) => {
    let output = [0];
    for (let i = 1; i < input.length; i++) {
      output.push(input[i] - input[i - 1]);
    }
    return output;
  };

  const calculateR0 = (input) => {
    let output = [0];
    for (let i = 1; i < input.length; i++) {
      if (input[i - 1] == 0) {
        output.push(0);
      } else {
        output.push(round(input[i] / input[i - 1]));
      }
    }
    return output;
  };

  const calcR0average = (input) => {
    let output = 0;
    for (let i of input) {
      output += i;
    }

    return output / input.length;
  };

  let loading = true;

  onMount(async () => {
    // Fetch data
    let response;
    try {
      //response = await fetch(
       // "https://api.jsonbin.io/b/5ea771be66e603359fdfd330/latest",
        //{
         // method: "GET",
          //headers: {
           // "secret-key":
      //         "$2b$10$iDj21mWHRqHDHxbScv4fR.0/.C2Iece5C4.eykPNV3jKXRMlxbBiO",
      //     },
      //   }
      // );

       response = await fetch("https://www.koronavirus.hr/json/?action=podaci", {mode: 'cors'});

       let json = await response.json();

      /*
      let json = [
        {
          SlucajeviSvijet: 13727388,
          SlucajeviHrvatska: 4039,
          UmrliSvijet: 587675,
          UmrliHrvatska: 120,
          IzlijeceniSvijet: 8178042,
          IzlijeceniHrvatska: 2729,
          Datum: "2020-07-16 14:05",
        },
        {
          SlucajeviSvijet: 13495134,
          SlucajeviHrvatska: 3953,
          UmrliSvijet: 582125,
          UmrliHrvatska: 120,
          IzlijeceniSvijet: 7880340,
          IzlijeceniHrvatska: 2629,
          Datum: "2020-07-15 14:03",
        },
        {
          SlucajeviSvijet: 13266241,
          SlucajeviHrvatska: 3827,
          UmrliSvijet: 576285,
          UmrliHrvatska: 120,
          IzlijeceniSvijet: 7732929,
          IzlijeceniHrvatska: 2558,
          Datum: "2020-07-14 13:51",
        },
        {
          SlucajeviSvijet: 13062585,
          SlucajeviHrvatska: 3775,
          UmrliSvijet: 572227,
          UmrliHrvatska: 119,
          IzlijeceniSvijet: 7611105,
          IzlijeceniHrvatska: 2514,
          Datum: "2020-07-13 13:51",
        },
        {
          SlucajeviSvijet: 12869502,
          SlucajeviHrvatska: 3722,
          UmrliSvijet: 568298,
          UmrliHrvatska: 119,
          IzlijeceniSvijet: 7501400,
          IzlijeceniHrvatska: 2486,
          Datum: "2020-07-12 14:03",
        },
        {
          SlucajeviSvijet: 12653888,
          SlucajeviHrvatska: 3672,
          UmrliSvijet: 563518,
          UmrliHrvatska: 118,
          IzlijeceniSvijet: 7387778,
          IzlijeceniHrvatska: 2466,
          Datum: "2020-07-11 14:03",
        },
        {
          SlucajeviSvijet: 12420013,
          SlucajeviHrvatska: 3532,
          UmrliSvijet: 558090,
          UmrliHrvatska: 117,
          IzlijeceniSvijet: 7246128,
          IzlijeceniHrvatska: 2377,
          Datum: "2020-07-10 14:33",
        },
        {
          SlucajeviSvijet: 12193804,
          SlucajeviHrvatska: 3416,
          UmrliSvijet: 552536,
          UmrliHrvatska: 115,
          IzlijeceniSvijet: 7090364,
          IzlijeceniHrvatska: 2323,
          Datum: "2020-07-09 14:02",
        },
        {
          SlucajeviSvijet: 11976310,
          SlucajeviHrvatska: 3325,
          UmrliSvijet: 547142,
          UmrliHrvatska: 114,
          IzlijeceniSvijet: 6920841,
          IzlijeceniHrvatska: 2277,
          Datum: "2020-07-08 14:10",
        },
        {
          SlucajeviSvijet: 11769250,
          SlucajeviHrvatska: 3272,
          UmrliSvijet: 541487,
          UmrliHrvatska: 113,
          IzlijeceniSvijet: 6762709,
          IzlijeceniHrvatska: 2229,
          Datum: "2020-07-07 14:01",
        },
        {
          SlucajeviSvijet: 11584921,
          SlucajeviHrvatska: 3220,
          UmrliSvijet: 537362,
          UmrliHrvatska: 113,
          IzlijeceniSvijet: 6552292,
          IzlijeceniHrvatska: 2210,
          Datum: "2020-07-06 14:03",
        },
        {
          SlucajeviSvijet: 11410090,
          SlucajeviHrvatska: 3151,
          UmrliSvijet: 534159,
          UmrliHrvatska: 113,
          IzlijeceniSvijet: 6458859,
          IzlijeceniHrvatska: 2196,
          Datum: "2020-07-05 13:58",
        },
        {
          SlucajeviSvijet: 11216481,
          SlucajeviHrvatska: 3094,
          UmrliSvijet: 529526,
          UmrliHrvatska: 113,
          IzlijeceniSvijet: 6362872,
          IzlijeceniHrvatska: 2183,
          Datum: "2020-07-04 14:01",
        },
        {
          SlucajeviSvijet: 11016328,
          SlucajeviHrvatska: 3008,
          UmrliSvijet: 524748,
          UmrliHrvatska: 112,
          IzlijeceniSvijet: 6172791,
          IzlijeceniHrvatska: 2168,
          Datum: "2020-07-03 14:04",
        },
        {
          SlucajeviSvijet: 10834202,
          SlucajeviHrvatska: 2912,
          UmrliSvijet: 519582,
          UmrliHrvatska: 110,
          IzlijeceniSvijet: 6054005,
          IzlijeceniHrvatska: 2155,
          Datum: "2020-07-02 13:54",
        },
        {
          SlucajeviSvijet: 10614903,
          SlucajeviHrvatska: 2831,
          UmrliSvijet: 514628,
          UmrliHrvatska: 108,
          IzlijeceniSvijet: 5824883,
          IzlijeceniHrvatska: 2155,
          Datum: "2020-07-01 13:38",
        },
        {
          SlucajeviSvijet: 10435321,
          SlucajeviHrvatska: 2777,
          UmrliSvijet: 508810,
          UmrliHrvatska: 107,
          IzlijeceniSvijet: 5692248,
          IzlijeceniHrvatska: 2155,
          Datum: "2020-06-30 13:58",
        },
        {
          SlucajeviSvijet: 10272143,
          SlucajeviHrvatska: 2725,
          UmrliSvijet: 504965,
          UmrliHrvatska: 107,
          IzlijeceniSvijet: 5573549,
          IzlijeceniHrvatska: 2155,
          Datum: "2020-06-29 13:54",
        },
        {
          SlucajeviSvijet: 10110867,
          SlucajeviHrvatska: 2691,
          UmrliSvijet: 501878,
          UmrliHrvatska: 107,
          IzlijeceniSvijet: 5483527,
          IzlijeceniHrvatska: 2152,
          Datum: "2020-06-28 14:03",
        },
        {
          SlucajeviSvijet: 9935207,
          SlucajeviHrvatska: 2624,
          UmrliSvijet: 497551,
          UmrliHrvatska: 107,
          IzlijeceniSvijet: 5383817,
          IzlijeceniHrvatska: 2152,
          Datum: "2020-06-27 13:51",
        },
        {
          SlucajeviSvijet: 9737426,
          SlucajeviHrvatska: 2539,
          UmrliSvijet: 492387,
          UmrliHrvatska: 107,
          IzlijeceniSvijet: 5269804,
          IzlijeceniHrvatska: 2150,
          Datum: "2020-06-26 14:06",
        },
        {
          SlucajeviSvijet: 9559350,
          SlucajeviHrvatska: 2483,
          UmrliSvijet: 485613,
          UmrliHrvatska: 107,
          IzlijeceniSvijet: 5198559,
          IzlijeceniHrvatska: 2149,
          Datum: "2020-06-25 13:47",
        },
        {
          SlucajeviSvijet: 9380725,
          SlucajeviHrvatska: 2388,
          UmrliSvijet: 480396,
          UmrliHrvatska: 107,
          IzlijeceniSvijet: 5068402,
          IzlijeceniHrvatska: 2145,
          Datum: "2020-06-24 13:57",
        },
        {
          SlucajeviSvijet: 9218408,
          SlucajeviHrvatska: 2366,
          UmrliSvijet: 474960,
          UmrliHrvatska: 107,
          IzlijeceniSvijet: 4961937,
          IzlijeceniHrvatska: 2142,
          Datum: "2020-06-23 13:29",
        },
        {
          SlucajeviSvijet: 9067906,
          SlucajeviHrvatska: 2336,
          UmrliSvijet: 471042,
          UmrliHrvatska: 107,
          IzlijeceniSvijet: 4850752,
          IzlijeceniHrvatska: 2142,
          Datum: "2020-06-22 14:09",
        },
        {
          SlucajeviSvijet: 8943566,
          SlucajeviHrvatska: 2317,
          UmrliSvijet: 467272,
          UmrliHrvatska: 107,
          IzlijeceniSvijet: 4756684,
          IzlijeceniHrvatska: 2142,
          Datum: "2020-06-21 14:04",
        },
        {
          SlucajeviSvijet: 8787036,
          SlucajeviHrvatska: 2299,
          UmrliSvijet: 463157,
          UmrliHrvatska: 107,
          IzlijeceniSvijet: 4647155,
          IzlijeceniHrvatska: 2142,
          Datum: "2020-06-20 13:51",
        },
        {
          SlucajeviSvijet: 8608595,
          SlucajeviHrvatska: 2280,
          UmrliSvijet: 456955,
          UmrliHrvatska: 107,
          IzlijeceniSvijet: 4559057,
          IzlijeceniHrvatska: 2142,
          Datum: "2020-06-19 13:34",
        },
        {
          SlucajeviSvijet: 8467178,
          SlucajeviHrvatska: 2269,
          UmrliSvijet: 451954,
          UmrliHrvatska: 107,
          IzlijeceniSvijet: 4439352,
          IzlijeceniHrvatska: 2142,
          Datum: "2020-06-18 14:11",
        },
        {
          SlucajeviSvijet: 8283912,
          SlucajeviHrvatska: 2258,
          UmrliSvijet: 446543,
          UmrliHrvatska: 107,
          IzlijeceniSvijet: 4339588,
          IzlijeceniHrvatska: 2141,
          Datum: "2020-06-17 14:01",
        },
        {
          SlucajeviSvijet: 8137913,
          SlucajeviHrvatska: 2255,
          UmrliSvijet: 439585,
          UmrliHrvatska: 107,
          IzlijeceniSvijet: 4249501,
          IzlijeceniHrvatska: 2140,
          Datum: "2020-06-16 13:50",
        },
        {
          SlucajeviSvijet: 8017847,
          SlucajeviHrvatska: 2254,
          UmrliSvijet: 436125,
          UmrliHrvatska: 107,
          IzlijeceniSvijet: 4140718,
          IzlijeceniHrvatska: 2140,
          Datum: "2020-06-15 13:59",
        },
        {
          SlucajeviSvijet: 7895921,
          SlucajeviHrvatska: 2252,
          UmrliSvijet: 432882,
          UmrliHrvatska: 107,
          IzlijeceniSvijet: 4056222,
          IzlijeceniHrvatska: 2134,
          Datum: "2020-06-14 13:18",
        },
        {
          SlucajeviSvijet: 7765626,
          SlucajeviHrvatska: 2251,
          UmrliSvijet: 428745,
          UmrliHrvatska: 107,
          IzlijeceniSvijet: 3983706,
          IzlijeceniHrvatska: 2134,
          Datum: "2020-06-13 13:52",
        },
        {
          SlucajeviSvijet: 7622027,
          SlucajeviHrvatska: 2249,
          UmrliSvijet: 424325,
          UmrliHrvatska: 107,
          IzlijeceniSvijet: 3860314,
          IzlijeceniHrvatska: 2133,
          Datum: "2020-06-12 13:12",
        },
        {
          SlucajeviSvijet: 7482804,
          SlucajeviHrvatska: 2249,
          UmrliSvijet: 419494,
          UmrliHrvatska: 106,
          IzlijeceniSvijet: 3798160,
          IzlijeceniHrvatska: 2132,
          Datum: "2020-06-11 13:21",
        },
        {
          SlucajeviSvijet: 7343077,
          SlucajeviHrvatska: 2249,
          UmrliSvijet: 414126,
          UmrliHrvatska: 106,
          IzlijeceniSvijet: 3620284,
          IzlijeceniHrvatska: 2130,
          Datum: "2020-06-10 13:54",
        },
        {
          SlucajeviSvijet: 7222643,
          SlucajeviHrvatska: 2247,
          UmrliSvijet: 409242,
          UmrliHrvatska: 106,
          IzlijeceniSvijet: 3557783,
          IzlijeceniHrvatska: 2130,
          Datum: "2020-06-09 13:49",
        },
        {
          SlucajeviSvijet: 7110306,
          SlucajeviHrvatska: 2247,
          UmrliSvijet: 406474,
          UmrliHrvatska: 104,
          IzlijeceniSvijet: 3469010,
          IzlijeceniHrvatska: 2128,
          Datum: "2020-06-08 13:42",
        },
        {
          SlucajeviSvijet: 7005471,
          SlucajeviHrvatska: 2247,
          UmrliSvijet: 402669,
          UmrliHrvatska: 104,
          IzlijeceniSvijet: 3427040,
          IzlijeceniHrvatska: 2126,
          Datum: "2020-06-07 13:38",
        },
        {
          SlucajeviSvijet: 6874796,
          SlucajeviHrvatska: 2247,
          UmrliSvijet: 398682,
          UmrliHrvatska: 104,
          IzlijeceniSvijet: 3368987,
          IzlijeceniHrvatska: 2121,
          Datum: "2020-06-06 13:40",
        },
        {
          SlucajeviSvijet: 6726982,
          SlucajeviHrvatska: 2247,
          UmrliSvijet: 393616,
          UmrliHrvatska: 103,
          IzlijeceniSvijet: 3269790,
          IzlijeceniHrvatska: 2113,
          Datum: "2020-06-05 13:14",
        },
        {
          SlucajeviSvijet: 6596048,
          SlucajeviHrvatska: 2247,
          UmrliSvijet: 388419,
          UmrliHrvatska: 103,
          IzlijeceniSvijet: 3187862,
          IzlijeceniHrvatska: 2105,
          Datum: "2020-06-04 13:45",
        },
        {
          SlucajeviSvijet: 6474784,
          SlucajeviHrvatska: 2246,
          UmrliSvijet: 382923,
          UmrliHrvatska: 103,
          IzlijeceniSvijet: 3083701,
          IzlijeceniHrvatska: 2095,
          Datum: "2020-06-03 13:34",
        },
        {
          SlucajeviSvijet: 6393784,
          SlucajeviHrvatska: 2246,
          UmrliSvijet: 377964,
          UmrliHrvatska: 103,
          IzlijeceniSvijet: 2926669,
          IzlijeceniHrvatska: 2088,
          Datum: "2020-06-02 13:52",
        },
        {
          SlucajeviSvijet: 6290758,
          SlucajeviHrvatska: 2246,
          UmrliSvijet: 374336,
          UmrliHrvatska: 103,
          IzlijeceniSvijet: 2862119,
          IzlijeceniHrvatska: 2077,
          Datum: "2020-06-01 13:49",
        },
        {
          SlucajeviSvijet: 6179213,
          SlucajeviHrvatska: 2246,
          UmrliSvijet: 371297,
          UmrliHrvatska: 103,
          IzlijeceniSvijet: 2747414,
          IzlijeceniHrvatska: 2072,
          Datum: "2020-05-31 14:02",
        },
        {
          SlucajeviSvijet: 6052421,
          SlucajeviHrvatska: 2246,
          UmrliSvijet: 367288,
          UmrliHrvatska: 103,
          IzlijeceniSvijet: 2675408,
          IzlijeceniHrvatska: 2063,
          Datum: "2020-05-30 13:40",
        },
        {
          SlucajeviSvijet: 5925928,
          SlucajeviHrvatska: 2245,
          UmrliSvijet: 362555,
          UmrliHrvatska: 103,
          IzlijeceniSvijet: 2593700,
          IzlijeceniHrvatska: 2059,
          Datum: "2020-05-29 14:07",
        },
        {
          SlucajeviSvijet: 5813289,
          SlucajeviHrvatska: 2245,
          UmrliSvijet: 357896,
          UmrliHrvatska: 102,
          IzlijeceniSvijet: 2514966,
          IzlijeceniHrvatska: 2051,
          Datum: "2020-05-28 14:07",
        },
        {
          SlucajeviSvijet: 5709517,
          SlucajeviHrvatska: 2244,
          UmrliSvijet: 352748,
          UmrliHrvatska: 101,
          IzlijeceniSvijet: 2450995,
          IzlijeceniHrvatska: 2047,
          Datum: "2020-05-27 14:12",
        },
        {
          SlucajeviSvijet: 5609644,
          SlucajeviHrvatska: 2244,
          UmrliSvijet: 348319,
          UmrliHrvatska: 101,
          IzlijeceniSvijet: 2386485,
          IzlijeceniHrvatska: 2046,
          Datum: "2020-05-26 14:08",
        },
        {
          SlucajeviSvijet: 5520738,
          SlucajeviHrvatska: 2244,
          UmrliSvijet: 347015,
          UmrliHrvatska: 100,
          IzlijeceniSvijet: 2313223,
          IzlijeceniHrvatska: 2035,
          Datum: "2020-05-25 14:11",
        },
        {
          SlucajeviSvijet: 5428247,
          SlucajeviHrvatska: 2244,
          UmrliSvijet: 344422,
          UmrliHrvatska: 99,
          IzlijeceniSvijet: 2259809,
          IzlijeceniHrvatska: 2027,
          Datum: "2020-05-24 14:00",
        },
        {
          SlucajeviSvijet: 5324245,
          SlucajeviHrvatska: 2243,
          UmrliSvijet: 340324,
          UmrliHrvatska: 99,
          IzlijeceniSvijet: 2172636,
          IzlijeceniHrvatska: 2023,
          Datum: "2020-05-23 14:04",
        },
        {
          SlucajeviSvijet: 5219900,
          SlucajeviHrvatska: 2243,
          UmrliSvijet: 335108,
          UmrliHrvatska: 99,
          IzlijeceniSvijet: 2097138,
          IzlijeceniHrvatska: 2011,
          Datum: "2020-05-22 14:04",
        },
        {
          SlucajeviSvijet: 5109043,
          SlucajeviHrvatska: 2237,
          UmrliSvijet: 330255,
          UmrliHrvatska: 97,
          IzlijeceniSvijet: 2037834,
          IzlijeceniHrvatska: 1978,
          Datum: "2020-05-21 14:03",
        },
        {
          SlucajeviSvijet: 5008368,
          SlucajeviHrvatska: 2234,
          UmrliSvijet: 325325,
          UmrliHrvatska: 96,
          IzlijeceniSvijet: 1974974,
          IzlijeceniHrvatska: 1978,
          Datum: "2020-05-20 14:04",
        },
        {
          SlucajeviSvijet: 4911720,
          SlucajeviHrvatska: 2232,
          UmrliSvijet: 320454,
          UmrliHrvatska: 96,
          IzlijeceniSvijet: 1919656,
          IzlijeceniHrvatska: 1967,
          Datum: "2020-05-19 14:08",
        },
        {
          SlucajeviSvijet: 4821725,
          SlucajeviHrvatska: 2228,
          UmrliSvijet: 317011,
          UmrliHrvatska: 95,
          IzlijeceniSvijet: 1866581,
          IzlijeceniHrvatska: 1946,
          Datum: "2020-05-18 14:04",
        },
        {
          SlucajeviSvijet: 4740536,
          SlucajeviHrvatska: 2226,
          UmrliSvijet: 313641,
          UmrliHrvatska: 95,
          IzlijeceniSvijet: 1824336,
          IzlijeceniHrvatska: 1936,
          Datum: "2020-05-17 14:06",
        },
        {
          SlucajeviSvijet: 4649077,
          SlucajeviHrvatska: 2224,
          UmrliSvijet: 309047,
          UmrliHrvatska: 95,
          IzlijeceniSvijet: 1771755,
          IzlijeceniHrvatska: 1913,
          Datum: "2020-05-16 14:24",
        },
        {
          SlucajeviSvijet: 4546070,
          SlucajeviHrvatska: 2222,
          UmrliSvijet: 303863,
          UmrliHrvatska: 95,
          IzlijeceniSvijet: 1716115,
          IzlijeceniHrvatska: 1869,
          Datum: "2020-05-15 14:11",
        },
        {
          SlucajeviSvijet: 4452820,
          SlucajeviHrvatska: 2221,
          UmrliSvijet: 298737,
          UmrliHrvatska: 94,
          IzlijeceniSvijet: 1675943,
          IzlijeceniHrvatska: 1850,
          Datum: "2020-05-14 14:04",
        },
        {
          SlucajeviSvijet: 4363906,
          SlucajeviHrvatska: 2213,
          UmrliSvijet: 293547,
          UmrliHrvatska: 94,
          IzlijeceniSvijet: 1613346,
          IzlijeceniHrvatska: 1834,
          Datum: "2020-05-13 14:11",
        },
        {
          SlucajeviSvijet: 4274648,
          SlucajeviHrvatska: 2207,
          UmrliSvijet: 287670,
          UmrliHrvatska: 91,
          IzlijeceniSvijet: 1537301,
          IzlijeceniHrvatska: 1808,
          Datum: "2020-05-12 14:04",
        },
        {
          SlucajeviSvijet: 4207908,
          SlucajeviHrvatska: 2196,
          UmrliSvijet: 284382,
          UmrliHrvatska: 91,
          IzlijeceniSvijet: 1504561,
          IzlijeceniHrvatska: 1784,
          Datum: "2020-05-11 14:13",
        },
        {
          SlucajeviSvijet: 4122912,
          SlucajeviHrvatska: 2187,
          UmrliSvijet: 280882,
          UmrliHrvatska: 90,
          IzlijeceniSvijet: 1451620,
          IzlijeceniHrvatska: 1764,
          Datum: "2020-05-10 14:05",
        },
        {
          SlucajeviSvijet: 4033320,
          SlucajeviHrvatska: 2176,
          UmrliSvijet: 276686,
          UmrliHrvatska: 87,
          IzlijeceniSvijet: 1400009,
          IzlijeceniHrvatska: 1726,
          Datum: "2020-05-09 14:07",
        },
        {
          SlucajeviSvijet: 3936291,
          SlucajeviHrvatska: 2161,
          UmrliSvijet: 271181,
          UmrliHrvatska: 86,
          IzlijeceniSvijet: 1351299,
          IzlijeceniHrvatska: 1689,
          Datum: "2020-05-08 14:12",
        },
        {
          SlucajeviSvijet: 3843524,
          SlucajeviHrvatska: 2125,
          UmrliSvijet: 265668,
          UmrliHrvatska: 86,
          IzlijeceniSvijet: 1314586,
          IzlijeceniHrvatska: 1641,
          Datum: "2020-05-07 14:09",
        },
        {
          SlucajeviSvijet: 3747504,
          SlucajeviHrvatska: 2119,
          UmrliSvijet: 258974,
          UmrliHrvatska: 85,
          IzlijeceniSvijet: 1251032,
          IzlijeceniHrvatska: 1601,
          Datum: "2020-05-06 14:05",
        },
        {
          SlucajeviSvijet: 3664513,
          SlucajeviHrvatska: 2112,
          UmrliSvijet: 252758,
          UmrliHrvatska: 83,
          IzlijeceniSvijet: 1206013,
          IzlijeceniHrvatska: 1560,
          Datum: "2020-05-05 14:07",
        },
        {
          SlucajeviSvijet: 3585108,
          SlucajeviHrvatska: 2101,
          UmrliSvijet: 248655,
          UmrliHrvatska: 80,
          IzlijeceniSvijet: 1161994,
          IzlijeceniHrvatska: 1522,
          Datum: "2020-05-04 14:18",
        },
        {
          SlucajeviSvijet: 3502956,
          SlucajeviHrvatska: 2096,
          UmrliSvijet: 245082,
          UmrliHrvatska: 79,
          IzlijeceniSvijet: 1128958,
          IzlijeceniHrvatska: 1489,
          Datum: "2020-05-03 14:04",
        },
        {
          SlucajeviSvijet: 3418185,
          SlucajeviHrvatska: 2088,
          UmrliSvijet: 239923,
          UmrliHrvatska: 77,
          IzlijeceniSvijet: 1089238,
          IzlijeceniHrvatska: 1463,
          Datum: "2020-05-02 14:06",
        },
        {
          SlucajeviSvijet: 3325976,
          SlucajeviHrvatska: 2085,
          UmrliSvijet: 234501,
          UmrliHrvatska: 75,
          IzlijeceniSvijet: 1052111,
          IzlijeceniHrvatska: 1421,
          Datum: "2020-05-01 14:04",
        },
        {
          SlucajeviSvijet: 3237600,
          SlucajeviHrvatska: 2076,
          UmrliSvijet: 228819,
          UmrliHrvatska: 69,
          IzlijeceniSvijet: 1010080,
          IzlijeceniHrvatska: 1348,
          Datum: "2020-04-30 14:13",
        },
        {
          SlucajeviSvijet: 3152556,
          SlucajeviHrvatska: 2062,
          UmrliSvijet: 218491,
          UmrliHrvatska: 67,
          IzlijeceniSvijet: 964840,
          IzlijeceniHrvatska: 1288,
          Datum: "2020-04-29 14:08",
        },
        {
          SlucajeviSvijet: 3081410,
          SlucajeviHrvatska: 2047,
          UmrliSvijet: 212337,
          UmrliHrvatska: 63,
          IzlijeceniSvijet: 931836,
          IzlijeceniHrvatska: 1232,
          Datum: "2020-04-28 14:10",
        },
        {
          SlucajeviSvijet: 3018203,
          SlucajeviHrvatska: 2039,
          UmrliSvijet: 207733,
          UmrliHrvatska: 59,
          IzlijeceniSvijet: 894759,
          IzlijeceniHrvatska: 1166,
          Datum: "2020-04-27 14:08",
        },
        {
          SlucajeviSvijet: 2934670,
          SlucajeviHrvatska: 2030,
          UmrliSvijet: 203687,
          UmrliHrvatska: 55,
          IzlijeceniSvijet: 840773,
          IzlijeceniHrvatska: 1103,
          Datum: "2020-04-26 14:07",
        },
        {
          SlucajeviSvijet: 2846536,
          SlucajeviHrvatska: 2016,
          UmrliSvijet: 197859,
          UmrliHrvatska: 54,
          IzlijeceniSvijet: 811777,
          IzlijeceniHrvatska: 1034,
          Datum: "2020-04-25 14:08",
        },
        {
          SlucajeviSvijet: 2747053,
          SlucajeviHrvatska: 2009,
          UmrliSvijet: 191899,
          UmrliHrvatska: 51,
          IzlijeceniSvijet: 757427,
          IzlijeceniHrvatska: 982,
          Datum: "2020-04-24 14:11",
        },
        {
          SlucajeviSvijet: 2656671,
          SlucajeviHrvatska: 1981,
          UmrliSvijet: 185192,
          UmrliHrvatska: 50,
          IzlijeceniSvijet: 729878,
          IzlijeceniHrvatska: 883,
          Datum: "2020-04-23 14:13",
        },
        {
          SlucajeviSvijet: 2575199,
          SlucajeviHrvatska: 1950,
          UmrliSvijet: 178658,
          UmrliHrvatska: 48,
          IzlijeceniSvijet: 704067,
          IzlijeceniHrvatska: 869,
          Datum: "2020-04-22 14:04",
        },
        {
          SlucajeviSvijet: 2499546,
          SlucajeviHrvatska: 1908,
          UmrliSvijet: 171338,
          UmrliHrvatska: 48,
          IzlijeceniSvijet: 658068,
          IzlijeceniHrvatska: 801,
          Datum: "2020-04-21 14:08",
        },
        {
          SlucajeviSvijet: 2422286,
          SlucajeviHrvatska: 1881,
          UmrliSvijet: 165924,
          UmrliHrvatska: 47,
          IzlijeceniSvijet: 635761,
          IzlijeceniHrvatska: 771,
          Datum: "2020-04-20 14:09",
        },
        {
          SlucajeviSvijet: 2348953,
          SlucajeviHrvatska: 1871,
          UmrliSvijet: 161221,
          UmrliHrvatska: 47,
          IzlijeceniSvijet: 605718,
          IzlijeceniHrvatska: 709,
          Datum: "2020-04-19 14:07",
        },
        {
          SlucajeviSvijet: 2265291,
          SlucajeviHrvatska: 1832,
          UmrliSvijet: 154900,
          UmrliHrvatska: 39,
          IzlijeceniSvijet: 581325,
          IzlijeceniHrvatska: 615,
          Datum: "2020-04-18 14:05",
        },
        {
          SlucajeviSvijet: 2197161,
          SlucajeviHrvatska: 1814,
          UmrliSvijet: 147512,
          UmrliHrvatska: 36,
          IzlijeceniSvijet: 557617,
          IzlijeceniHrvatska: 600,
          Datum: "2020-04-17 14:06",
        },
        {
          SlucajeviSvijet: 2098981,
          SlucajeviHrvatska: 1791,
          UmrliSvijet: 135900,
          UmrliHrvatska: 35,
          IzlijeceniSvijet: 523753,
          IzlijeceniHrvatska: 529,
          Datum: "2020-04-16 14:12",
        },
        {
          SlucajeviSvijet: 2014554,
          SlucajeviHrvatska: 1741,
          UmrliSvijet: 127598,
          UmrliHrvatska: 33,
          IzlijeceniSvijet: 491842,
          IzlijeceniHrvatska: 473,
          Datum: "2020-04-15 15:30",
        },
        {
          SlucajeviSvijet: 1938786,
          SlucajeviHrvatska: 1704,
          UmrliSvijet: 120851,
          UmrliHrvatska: 31,
          IzlijeceniSvijet: 459131,
          IzlijeceniHrvatska: 415,
          Datum: "2020-04-14 14:17",
        },
        {
          SlucajeviSvijet: 1864666,
          SlucajeviHrvatska: 1650,
          UmrliSvijet: 115101,
          UmrliHrvatska: 25,
          IzlijeceniSvijet: 433909,
          IzlijeceniHrvatska: 400,
          Datum: "2020-04-13 14:07",
        },
        {
          SlucajeviSvijet: 1792779,
          SlucajeviHrvatska: 1600,
          UmrliSvijet: 109786,
          UmrliHrvatska: 23,
          IzlijeceniSvijet: 411590,
          IzlijeceniHrvatska: 373,
          Datum: "2020-04-12 14:06",
        },
        {
          SlucajeviSvijet: 1710798,
          SlucajeviHrvatska: 1534,
          UmrliSvijet: 103512,
          UmrliHrvatska: 21,
          IzlijeceniSvijet: 382073,
          IzlijeceniHrvatska: 323,
          Datum: "2020-04-11 14:07",
        },
        {
          SlucajeviSvijet: 1617576,
          SlucajeviHrvatska: 1495,
          UmrliSvijet: 96939,
          UmrliHrvatska: 21,
          IzlijeceniSvijet: 365789,
          IzlijeceniHrvatska: 231,
          Datum: "2020-04-10 14:06",
        },
        {
          SlucajeviSvijet: 1529975,
          SlucajeviHrvatska: 1407,
          UmrliSvijet: 89427,
          UmrliHrvatska: 20,
          IzlijeceniSvijet: 337276,
          IzlijeceniHrvatska: 219,
          Datum: "2020-04-09 14:08",
        },
        {
          SlucajeviSvijet: 1444822,
          SlucajeviHrvatska: 1343,
          UmrliSvijet: 83103,
          UmrliHrvatska: 19,
          IzlijeceniSvijet: 309113,
          IzlijeceniHrvatska: 179,
          Datum: "2020-04-08 14:05",
        },
        {
          SlucajeviSvijet: 1360232,
          SlucajeviHrvatska: 1282,
          UmrliSvijet: 75961,
          UmrliHrvatska: 18,
          IzlijeceniSvijet: 293615,
          IzlijeceniHrvatska: 167,
          Datum: "2020-04-07 14:06",
        },
        {
          SlucajeviSvijet: 1286294,
          SlucajeviHrvatska: 1222,
          UmrliSvijet: 70446,
          UmrliHrvatska: 16,
          IzlijeceniSvijet: 271882,
          IzlijeceniHrvatska: 130,
          Datum: "2020-04-06 14:05",
        },
        {
          SlucajeviSvijet: 1215940,
          SlucajeviHrvatska: 1182,
          UmrliSvijet: 65663,
          UmrliHrvatska: 15,
          IzlijeceniSvijet: 253689,
          IzlijeceniHrvatska: 125,
          Datum: "2020-04-05 14:12",
        },
        {
          SlucajeviSvijet: 1133455,
          SlucajeviHrvatska: 1126,
          UmrliSvijet: 60381,
          UmrliHrvatska: 12,
          IzlijeceniSvijet: 236000,
          IzlijeceniHrvatska: 119,
          Datum: "2020-04-04 14:04",
        },
        {
          SlucajeviSvijet: 1030642,
          SlucajeviHrvatska: 1079,
          UmrliSvijet: 54229,
          UmrliHrvatska: 8,
          IzlijeceniSvijet: 220011,
          IzlijeceniHrvatska: 92,
          Datum: "2020-04-03 14:04",
        },
        {
          SlucajeviSvijet: 950713,
          SlucajeviHrvatska: 1011,
          UmrliSvijet: 48313,
          UmrliHrvatska: 7,
          IzlijeceniSvijet: 202826,
          IzlijeceniHrvatska: 88,
          Datum: "2020-04-02 14:04",
        },
        {
          SlucajeviSvijet: 874607,
          SlucajeviHrvatska: 963,
          UmrliSvijet: 43429,
          UmrliHrvatska: 6,
          IzlijeceniSvijet: 184952,
          IzlijeceniHrvatska: 73,
          Datum: "2020-04-01 14:16",
        },
        {
          SlucajeviSvijet: 802941,
          SlucajeviHrvatska: 867,
          UmrliSvijet: 39022,
          UmrliHrvatska: 6,
          IzlijeceniSvijet: 172319,
          IzlijeceniHrvatska: 67,
          Datum: "2020-03-31 14:47",
        },
        {
          SlucajeviSvijet: 735336,
          SlucajeviHrvatska: 790,
          UmrliSvijet: 34818,
          UmrliHrvatska: 6,
          IzlijeceniSvijet: 156137,
          IzlijeceniHrvatska: 64,
          Datum: "2020-03-30 14:16",
        },
        {
          SlucajeviSvijet: 687905,
          SlucajeviHrvatska: 713,
          UmrliSvijet: 31771,
          UmrliHrvatska: 6,
          IzlijeceniSvijet: 146319,
          IzlijeceniHrvatska: 52,
          Datum: "2020-03-30 14:21",
        },
        {
          SlucajeviSvijet: 0,
          SlucajeviHrvatska: 657,
          UmrliSvijet: 0,
          UmrliHrvatska: 5,
          IzlijeceniSvijet: 0,
          IzlijeceniHrvatska: 49,
          Datum: "2020-03-29 15:44",
        },
        {
          SlucajeviSvijet: 558298,
          SlucajeviHrvatska: 586,
          UmrliSvijet: 25259,
          UmrliHrvatska: 3,
          IzlijeceniSvijet: 128717,
          IzlijeceniHrvatska: 37,
          Datum: "2020-03-27 16:04",
        },
        {
          SlucajeviSvijet: 492250,
          SlucajeviHrvatska: 495,
          UmrliSvijet: 22180,
          UmrliHrvatska: 3,
          IzlijeceniSvijet: 119732,
          IzlijeceniHrvatska: 22,
          Datum: "2020-03-27 16:17",
        },
        {
          SlucajeviSvijet: 441093,
          SlucajeviHrvatska: 442,
          UmrliSvijet: 19760,
          UmrliHrvatska: 1,
          IzlijeceniSvijet: 112036,
          IzlijeceniHrvatska: 22,
          Datum: "2020-03-25 17:12",
        },
        {
          SlucajeviSvijet: 396211,
          SlucajeviHrvatska: 382,
          UmrliSvijet: 17260,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 103748,
          IzlijeceniHrvatska: 16,
          Datum: "2020-03-25 10:52",
        },
        {
          SlucajeviSvijet: 341329,
          SlucajeviHrvatska: 315,
          UmrliSvijet: 14746,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 99040,
          IzlijeceniHrvatska: 5,
          Datum: "2020-03-25 12:40",
        },
        {
          SlucajeviSvijet: 318953,
          SlucajeviHrvatska: 254,
          UmrliSvijet: 13684,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 96006,
          IzlijeceniHrvatska: 5,
          Datum: "2020-03-23 08:54",
        },
        {
          SlucajeviSvijet: 287124,
          SlucajeviHrvatska: 206,
          UmrliSvijet: 11890,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 93611,
          IzlijeceniHrvatska: 5,
          Datum: "2020-03-21 16:07",
        },
        {
          SlucajeviSvijet: 255943,
          SlucajeviHrvatska: 128,
          UmrliSvijet: 10495,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 89918,
          IzlijeceniHrvatska: 5,
          Datum: "2020-03-20 16:16",
        },
        {
          SlucajeviSvijet: 219894,
          SlucajeviHrvatska: 105,
          UmrliSvijet: 8979,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 85769,
          IzlijeceniHrvatska: 5,
          Datum: "2020-03-19 19:32",
        },
        {
          SlucajeviSvijet: 217539,
          SlucajeviHrvatska: 89,
          UmrliSvijet: 8933,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 84383,
          IzlijeceniHrvatska: 5,
          Datum: "2020-03-18 22:33",
        },
        {
          SlucajeviSvijet: 189683,
          SlucajeviHrvatska: 69,
          UmrliSvijet: 7513,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 80874,
          IzlijeceniHrvatska: 5,
          Datum: "2020-03-17 23:17",
        },
        {
          SlucajeviSvijet: 175696,
          SlucajeviHrvatska: 57,
          UmrliSvijet: 6715,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 77868,
          IzlijeceniHrvatska: 4,
          Datum: "2020-03-16 17:07",
        },
        {
          SlucajeviSvijet: 156760,
          SlucajeviHrvatska: 49,
          UmrliSvijet: 6455,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 75937,
          IzlijeceniHrvatska: 3,
          Datum: "2020-03-15 21:52",
        },
        {
          SlucajeviSvijet: 145682,
          SlucajeviHrvatska: 37,
          UmrliSvijet: 5436,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 72533,
          IzlijeceniHrvatska: 2,
          Datum: "2020-03-14 09:30",
        },
        {
          SlucajeviSvijet: 139016,
          SlucajeviHrvatska: 32,
          UmrliSvijet: 5116,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 70727,
          IzlijeceniHrvatska: 1,
          Datum: "2020-03-13 20:30",
        },
        {
          SlucajeviSvijet: 0,
          SlucajeviHrvatska: 19,
          UmrliSvijet: 0,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 0,
          IzlijeceniHrvatska: 1,
          Datum: "2020-03-12 08:00",
        },
        {
          SlucajeviSvijet: 0,
          SlucajeviHrvatska: 16,
          UmrliSvijet: 0,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 0,
          IzlijeceniHrvatska: 0,
          Datum: "2020-03-11 08:00",
        },
        {
          SlucajeviSvijet: 0,
          SlucajeviHrvatska: 13,
          UmrliSvijet: 0,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 0,
          IzlijeceniHrvatska: 0,
          Datum: "2020-03-10 08:00",
        },
        {
          SlucajeviSvijet: 0,
          SlucajeviHrvatska: 12,
          UmrliSvijet: 0,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 0,
          IzlijeceniHrvatska: 0,
          Datum: "2020-03-09 08:00",
        },
        {
          SlucajeviSvijet: 0,
          SlucajeviHrvatska: 12,
          UmrliSvijet: 0,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 0,
          IzlijeceniHrvatska: 0,
          Datum: "2020-03-08 08:00",
        },
        {
          SlucajeviSvijet: 0,
          SlucajeviHrvatska: 11,
          UmrliSvijet: 0,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 0,
          IzlijeceniHrvatska: 0,
          Datum: "2020-03-07 08:00",
        },
        {
          SlucajeviSvijet: 0,
          SlucajeviHrvatska: 10,
          UmrliSvijet: 0,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 0,
          IzlijeceniHrvatska: 0,
          Datum: "2020-03-06 08:00",
        },
        {
          SlucajeviSvijet: 0,
          SlucajeviHrvatska: 10,
          UmrliSvijet: 0,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 0,
          IzlijeceniHrvatska: 0,
          Datum: "2020-03-05 08:00",
        },
        {
          SlucajeviSvijet: 0,
          SlucajeviHrvatska: 9,
          UmrliSvijet: 0,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 0,
          IzlijeceniHrvatska: 0,
          Datum: "2020-03-04 08:00",
        },
        {
          SlucajeviSvijet: 0,
          SlucajeviHrvatska: 9,
          UmrliSvijet: 0,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 0,
          IzlijeceniHrvatska: 0,
          Datum: "2020-03-03 08:00",
        },
        {
          SlucajeviSvijet: 0,
          SlucajeviHrvatska: 8,
          UmrliSvijet: 0,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 0,
          IzlijeceniHrvatska: 0,
          Datum: "2020-03-02 08:00",
        },
        {
          SlucajeviSvijet: 0,
          SlucajeviHrvatska: 7,
          UmrliSvijet: 0,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 0,
          IzlijeceniHrvatska: 0,
          Datum: "2020-03-01 08:00",
        },
        {
          SlucajeviSvijet: 0,
          SlucajeviHrvatska: 5,
          UmrliSvijet: 0,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 0,
          IzlijeceniHrvatska: 0,
          Datum: "2020-02-29 08:00",
        },
        {
          SlucajeviSvijet: 0,
          SlucajeviHrvatska: 3,
          UmrliSvijet: 0,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 0,
          IzlijeceniHrvatska: 0,
          Datum: "2020-02-28 08:00",
        },
        {
          SlucajeviSvijet: 0,
          SlucajeviHrvatska: 3,
          UmrliSvijet: 0,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 0,
          IzlijeceniHrvatska: 0,
          Datum: "2020-02-27 08:00",
        },
        {
          SlucajeviSvijet: 0,
          SlucajeviHrvatska: 2,
          UmrliSvijet: 0,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: 0,
          IzlijeceniHrvatska: 0,
          Datum: "2020-02-26 08:00",
        },
      ];
      */

      json = json.reverse();

      json = [
        {
          SlucajeviSvijet: null,
          SlucajeviHrvatska: 1,
          UmrliSvijet: null,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: null,
          IzlijeceniHrvatska: 0,
          Datum: "2020-02-25 08:00",
        },
        ...json,
      ];

      dateAxis = [];
      totalCases = [];
      recoveries = [];
      deaths = [];

      for (let record of json) {
        // dateAxis.push(record.day);
        // totalCases.push(record.total);
        // recoveries.push(record.recovered);
        // deaths.push(record.dead);

        let day = new Date(record.Datum);
        let total = record.SlucajeviHrvatska;
        let recovered = record.IzlijeceniHrvatska;
        let dead = record.UmrliHrvatska;

        dateAxis.push(day);
        totalCases.push(total);
        recoveries.push(recovered);
        deaths.push(dead);
      }

      lastUpdate = new Date(json[json.length - 1].Datum);

      // Active cases
      activeCases = [];

      for (let i in dateAxis) {
        let value = totalCases[i] - recoveries[i] - deaths[i];
        activeCases.push(value);
      }

      // Deltas
      totalCasesDelta = [...calculateDeltas(totalCases)];
      recoveriesDelta = [...calculateDeltas(recoveries)];
      deathsDelta = [...calculateDeltas(deaths)];
      activeCasesDelta = [...calculateDeltas(activeCases)];

      // R0
      totalCasesR0 = [...calculateR0(totalCasesDelta)];

      // Summary
      summaryData = [
        activeCases.slice(-1).pop(),
        recoveries.slice(-1).pop(),
        deaths.slice(-1).pop(),
      ];

      // R0 average
      r0avg = calcR0average(totalCasesR0);

      // Ready!
      loading = false;
    } catch (error) {
      err = error;
    }
  });

  function timeout(ms) {
    return new Promise((resolve) => setTimeout(resolve, ms));
  }
</script>

<style>
  :global(h1, h2, h3, h4, h5) {
    text-align: center;
  }
</style>

<TopAppBar variant="fixed">
  <Row>
    <Section>
      <Title>COVID-19 podaci</Title>
    </Section>
    <Section align="end">

      {#if !loading}
        <p style="padding-right: 20px; display: flex; align-items: center;">
          <i class="material-icons topbar-icon">
            refresh
          </i>

          <span style="margin-left: 6px">
            {new Intl.DateTimeFormat('hr', {
              month: 'narrow',
              day: 'numeric',
              hour: 'numeric',
              minute: 'numeric',
            }).format(lastUpdate)}
          </span>

        </p>
      {/if}

    </Section>
  </Row>
</TopAppBar>

<main style="padding-top: 100px">

  {#if !loading}
    <div>

      <Summary input={summaryData} />

      <CaseData
        {dateAxis}
        {totalCases}
        {recoveries}
        {deaths}
        {activeCases}
        {totalCasesDelta}
        {deathsDelta} />

      <Rzero {dateAxis} input={totalCasesR0} {r0avg} />

      <!-- <LogGraphs {dateAxis} {totalCases} {recoveries} {deaths} {activeCases} /> -->

      <TableView
        {totalCases}
        {totalCasesDelta}
        {totalCasesR0}
        {recoveries}
        {recoveriesDelta}
        {deaths}
        {deathsDelta}
        {activeCases}
        {activeCasesDelta}
        {dateAxis}
        {r0avg} />

      <Notice />
    </div>
  {:else if err}
    <div style="text-align: center">
      <h1>❌</h1>
      <h4>Dogodila se greška</h4>
      <p
        style="opacity: 0.5; margin-top: 1rem; font-size: 0.9em; font-family:
        monospace">
        {err}
      </p>
    </div>
  {:else}
    <Spinner />
  {/if}

  <footer
    style="margin: 2rem auto; padding: 2rem 0; text-align: center; background:
    transparent; font-size: 1rem;">

    <a
      style="text-decoration: none; font-size: 1rem"
      href="https://goranalkovic.com">
      Copyright Goran Alković 2020
    </a>

  </footer>

</main>
