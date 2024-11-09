<script lang="ts">
	import { onDestroy } from 'svelte';
	import scheduleStore from './schedule-store.js';
	import Calendar from './Calendar.svelte';
	import Scheduler from './Scheduler.svelte';
  
	// Define types for schedule and appointments
	type Appointment = {
	  id: number;
	  eventname: string;
	  time: string;
	  completed: boolean;
	};
  
	type Schedule = {
	  [key: string]: Appointment[];
	};
  
	// Define schedule with type Schedule
	let schedule: Schedule = {};
	const unsubscribe = scheduleStore.subscribe((currState: Schedule) => {
	  schedule = currState;
	});
  
	onDestroy(() => {
	  if (unsubscribe) unsubscribe();
	});
  
	let schedulerShowing = false;
	let dateID = "";
	let dateHeading = "";
  
	// Automatically update appointments based on the selected dateID
	$: appointments = schedule[dateID];
  
	const makeDateHeading = () => {
	  let dateAsHeading = dateID.replace(/_/g, " ");
	  let date = new Date(`${dateAsHeading}`);
	  dateHeading = date.toLocaleString("en-US", { day: 'numeric', month: 'long', year: 'numeric' });
	};
  
	// Define event type for handleScheduler
	const handleScheduler = (e: Event) => {
	  const target = e.target as HTMLElement;
	  dateID = target.dataset.dateid || ""; // Safely handle undefined dataset
	  makeDateHeading();
	  schedulerShowing = true;
	};
  
	const removeEmptyDate = () => {
	  if (schedule[dateID] && schedule[dateID].length === 0) {
		scheduleStore.update((currDataState: Schedule) => {
		  delete currDataState[dateID];
		  return currDataState;
		});
	  }
	};
  
	const closeScheduler = () => {
	  schedulerShowing = false;
	  removeEmptyDate();
	};
  
	// Define event type for setApptToSch
	const setApptToSch = (e: CustomEvent<{ hour: number; minutes: number; amOrPM: string; eventName: string }>) => {
	  let time = `${e.detail.hour}:${e.detail.minutes < 10 ? '0' + e.detail.minutes : e.detail.minutes}${e.detail.amOrPM}`;
	  let newAppt: Appointment = {
		id: Math.floor(Math.random() * 1000000),
		eventname: e.detail.eventName,
		time: time === ":0" ? "no time set" : time,
		completed: false
	  };
  
	  // Update schedule with the new appointment
	  if (!schedule[dateID]) {
		scheduleStore.update((currState: Schedule) => {
		  currState[dateID] = [newAppt];
		  return currState;
		});
	  } else {
		scheduleStore.update((currState: Schedule) => {
		  let currDayAppts = currState[dateID];
		  currState[dateID] = [...currDayAppts, newAppt];
		  return currState;
		});
	  }
	};
  
	$: console.log(schedule);
  </script>
  
  <main>
	<Calendar on:click={handleScheduler} {schedule} />
	{#if schedulerShowing}
	  <Scheduler
		on:modalClose={closeScheduler}
		on:addAppt={setApptToSch}
		{dateID}
		{dateHeading}
		{appointments}
	  />
	{/if}
  </main>
  
  <style>
	main { font-family: Verdana, sans-serif; }
  </style>
  