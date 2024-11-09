	<script>
		const date = new Date();

		const today = {
			dayNumber: date.getDate(),
			month: date.getMonth(),
			year: date.getFullYear(),
		};

		const monthNames = [
			"January", "February", "March", "April", "May", "June",
			"July", "August", "September", "October", "November", "December"
		];

		let monthIndex = today.month;
		 
		let year = today.year;

		$: month = monthNames[monthIndex];
		$: firstDayIndex = new Date(year, monthIndex, 1).getDay();
		$: numberOfDays = new Date(year, monthIndex + 1, 0).getDate();
		$: calendarCellsQty = numberOfDays + firstDayIndex;

		const goToNextMonth = () => {
			if (monthIndex >= 11) {
				monthIndex = 0;
				year += 1;
			} else {
				monthIndex += 1;
			}
		};

		const goToPrevMonth = () => {
			if (monthIndex <= 0) {
				monthIndex = 11;
				year -= 1;
			} else {
				monthIndex -= 1;
			}
		};
	</script>

	<div class="month">
		<ul>
			<li class="prev" on:click={goToPrevMonth}>&#10094;</li>
			<li class="next" on:click={goToNextMonth}>&#10095;</li>
			<li>{month}<br><span style="font-size:18px">{year}</span></li>
		</ul>
	</div>

	<ul class="weekdays">
		<li>Su</li>
		<li>Mo</li>
		<li>Tu</li>
		<li>We</li>
		<li>Th</li>
		<li>Fr</li>
		<li>Sa</li>
	</ul>

	<ul class="days">
		{#each Array(calendarCellsQty) as _, i}
			{#if i < firstDayIndex || i >= numberOfDays + firstDayIndex}
				<li>&nbsp;</li>
			{:else}
				<li class:active={i === today.dayNumber + firstDayIndex - 1 &&
													monthIndex === today.month &&
													year === today.year}>
					{(i - firstDayIndex) + 1}
				</li>
			{/if}
		{/each}
	</ul>

	<style>
		ul { list-style-type: none; }

		.month {
			padding: 70px 25px;
			width: auto;
			background: #1abc9c;
			text-align: center;
		}

		.month ul { margin: 0; padding: 0; }

		.month ul li {
			color: white;
			font-size: 20px;
			text-transform: uppercase;
			letter-spacing: 3px;
		}

		.month .prev, .month .next {
			cursor: pointer;
		}

		.prev { float: left; padding-top: 10px; }
		.next { float: right; padding-top: 10px; }

		.weekdays {
			margin: 0;
			padding: 10px 0;
			background-color: #ddd;
		}

		.weekdays li {
			display: inline-block;
			width: 13.6%;
			color: #666;
			text-align: center;
		}

		.days {
			padding: 10px 0;
			background: #eee;
			margin: 0;
		}

		.days li {
			display: inline-block;
			border: 1px solid black;
			padding: 9px;
			width: 11.6%;
			text-align: center;
			margin-bottom: 1px;
			font-size: 1.2rem;
			color: #777;
			cursor: pointer;
		}

		.active {
			padding: 5px;
			background: #749c94;
			color: white !important;
		}
	</style>
