# plutanium-event-calendar
Plutanium Event Calendar is the most comprehensive event calendar for restaurants, sidebars, calendar widgets, order forms with appointment or timeslot picker, event list. Written in jQuery, usable with AngularJS.

Only API is open sourced at the moment. If you are interested in the library let me know!

##Features

###frontend features
 * can be integrated with you app or website
 * seamless autorefresh on background
 * single moment, allday and multiday events support
 * uses CSS flex model
 * can be used for reservations – available/full/nonworking day colors
 * set the boundaries user can navigate in ()
 * easily translate to your language
 * it properly handles timezones and daylight saving time (summer time)


###developer features
 * written in jQuery, stable, used in production
 * compatible with AngularJS and moment.js
 * fully customizable eventList content and behavior
 * generate JSON dynamically on client side or server side 
 * you can connect it to your backend app, custom Wordpress pluginm, or indirectly to any external service like Google Calendar etc.
 * show standard business hours on top of the event list
 * multiple calendar instances with different settings on a same webpage possible

##Use cases

###Restaurant, Hotel, Club organizing events for people
 * show business hours for every day in event feed header
 * show WE'RE CLOSED message on top of event feed on days you are out of business.
 * set your standard week business days in the settings, provide additional "We're closed today" events in JSON feed to close anytime.

###Online ordering system timeslot picker
 * show available/full appointments, nonworking days
 * use it as a date picker for your order form
 * lock calendar and highlight the ordered appointment

###Blog
 * show your blog posts in a calendar widget
 * show your blog posts in a calendar widget, together with your public google calendar feed and facebook feed

###Universal date od datetime slot picker
 * generate json dynamically on client side


##Getting started

###Browser

```html
<link rel="stylesheet" href="css/eventCalendar.css" type="text/css" media="all">
<link rel="stylesheet" href="css/eventCalendarTheme.css" type="text/css" media="all">

<script src="js/moment-with-locales.min.js"></script>
<script src="js/moment-timezone.js"></script>
<script src="js/twix.min.js"></script>
<script src="js/plutanium-event-calendar.js"></script>

<div id='eventCalendarWidget'></div>
```

###Your javascript file

```js
ecMainInstance = jQuery('#eventCalendarWidget').eventCalendar(options)
```



##Object format

###event object
Object containting a single event

<table class="table table-bordered table-striped bs-events-table">
	<thead>
		<tr>
			<th>JSON field</th>
			<th>Description</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>startDate</td>
			<td>start datetime in ISO 8601 format.</td>
		</tr>
		<tr>
			<td>endDate</td>
			<td>same as startDate if event has no duration</td>
		</tr>
		<tr>
			<td>id</td>
			<td>Every event has to contain an `id` property and it must be unique in JSON feed.</td>
		</tr>
		<tr>
			<td>type</td>
			<td>You can use `type` to identify and separate events to groups, or to identify appointment slot current status.</td>
		</tr>
		<tr>
			<td>description</td>
			<td>Any string describing the event.</td>
		</tr>
	</tbody>
</table>

For restaurant/hotel/club organizing events, you can use this format:
```js
Object
	{
	startDate: "2016-04-01T18:30:00+0000",
	endDate: "2016-04-01T23:00:00+0000",
	id: 28,
	type: "available",
	description: "Apple 40th Anniversary party"
	url: "http://mysite.com/events/url-to-event/"
	}
```

```js
Object
	{
	startDate: "2016-12-31T00:00:00+0000", // currently, 00:00:00 startDate means all-day event, considering to make it more clear
	endDate: "2016-12-31T00:00:00+0000",
	id: 29,
	type: "closed",
	description: "Private party" // Reason for being of of business this day.
	}
```

For appointment reservation slots/order systems, you can use this format:
```js
Object
	{
	startDate: "2016-04-24T13:45:00+0000",
	endDate: "2016-04-24T20:00:00+0000",
	id: 36,
	type: "available"
	}
```

where `type = available | full`

**Every event has to contain an `id` property and it must be unique in JSON feed.**

###eventsStorage object
Array of `event` objects.
```js
[ Object, Object, Object, Object, Object, ... ]
```

**Every event has to contain an `id` property and it must be unique in JSON feed.**

##Public methods

###refresh
Redownload JSON data and repaint calendar&eventlist.
```js
pecMainInstance.refresh()
```

###repaint
just html refresh, this does not redownload data
```js
pecMainInstance.repaint()
```

###switchView
Jump to month or day.
```js
pecMainInstance.switchView(jumpType, datetime, forceRefresh = false)
```

`jumpType` of `month` only jumps to provided month; `jumpType` of `day` is more specific, it jumps to the provided month and selects the day in the month view too. 
`datetime` must be a moment() object.
`forceRefresh` if true, it redownloads JSON data. Default false.

```js
pecMainInstance.switchView(moment("2016-07-22T13:45:00+0000"), "month") // jumps to July 2016
pecMainInstance.switchView(moment("2016-07-22T13:45:00+0000"), "day")   // jumps to July 2016 and selects 2016-07-22
```

###disableEventSelection
Locks event list. User can't select the event by mouse click.
```js
pecMainInstance.disableEventSelection()
```

###enableEventSelection
Enables user selection in event list. User can select the event by mouse click.
```js
pecMainInstance.enableEventSelection()
```

You probably want to handle user clicks too, see `clickedOnEventInEventListCallback()`.

###selectEvent
Select event by its ID. It is similar to user clicks on item in eventlist.

```js
pecMainInstance.selectEvent(id)
```

Returns `true` if successful.
Returns `false` if the event ID does not exists in eventsStorage.

The selected event may not be visible.
In typical usecase, you may also want the calendar and eventlist to jump to the date, to see the selected event in eventlist. Then call:

```js
pecMainInstance.switchView('day', datetime), resp. switchView('day', moment(getSelectedEvent().startDate))
```

###getEvent

```js
ecMainInstance.getEvent()
```

Returns an `event` object

###getSelectedEvent

```js
ecMainInstance.getSelectedEvent()
```

Returns an `event` object

###getEventStorage

```js
ecMainInstance.getEventsStorage()
```

Returns array of `event` objects.
```js
[ Object, Object, Object, Object, Object, ... ]
```

###removeEventSelection
Removes a userclick selection in eventList.

```js
ecMainInstance.removeEventSelection()
```

###highlightEvent
oznaci termin jako jako objednany, uzivatel muze klikat jinam ale zvyrazneni zustava. Obvykle potom jeste zamces kalendar proti dalsimu klikani pres disableEventSelection().

```js
pecMainInstance.highlightEvent(datetime, repaintViewNow, repaintEventListNow)
```

`datetime` must be a moment() object.

##Callbacks

###switchedViewCallback
Called for 
```js
smartEventDescription: function (event, eventIsHighlighted)
	{
	if (eventIsHighlighted)
		return "Your ordered appointment.";

	if (event.type == "full")
		return "Full";

	if (event.type == "available")
		return "Available";
	
	return event.description; // otherwise use description from json feed
	}
```

when you develop you can call it manually by

```js
pecMainInstance.switchedViewCallback()
```

###clickedOnEventInEventListCallback
This fires whenever a user click in eventList when calendar is unlocked. You can use this for your GUI - for example, unlock button triple-blink, change date button triple-blink etc.

when you develop you can call it manually by

```js
pecMainInstance.clickedOnEventInEventListCallback()
```

###refreshCallback

when you develop you can call it manually by

```js
pecMainInstance.refreshCallback()
```

###smartEventDescription

when you develop you can call it manually by

```js
pecMainInstance.smartEventDescription()
```


##Handle events
<table class="table table-bordered table-striped bs-events-table">
	<thead>
		<tr>
			<th>Event Type</th>
			<th>Description</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>newDataLoaded</td>
			<td>This event fires whenever JSON data were loaded or reloaded.</td>
		</tr>
		<tr>
			<td>eventListGenerated</td>
			<td>This event fires whenever eventList was repaint.</td>
		</tr>
		<tr>
			<td>blockedUserClick</td>
			<td>This event fires whenever a user clicked on eventList when calendar is locked.</td>
		</tr>
	</tbody>
</table>

###newDataLoaded

```js
jQuery('#eventCalendarLimit').one('newDataLoaded', function(event)
	{
	console.log("Events loaded from JSON for the first time, you can now switch views or highlight any event.");
	console.log(event)
	});
```

```js
jQuery('#eventCalendarLimit').on('newDataLoaded', function(event)
	{
	console.log("Events loaded from JSON, you can now switch views or highlight any event.");
	console.log(event)
	});
```

Protip: use `on()` for firing every time, `one()` for firing only for the first-time.

###eventListGenerated
This event fires whenever eventList was repaint.

```js
jQuery('#eventCalendarLimit').one('eventListGenerated', function(event)
	{
	console.log("Events loaded & displayed for the first time.")
	});
```

```js
jQuery('#eventCalendarLimit').on('eventListGenerated', function(event)
	{
	console.log("Events loaded & displayed. Fires also on every ajax refresh.")
	});
```

Protip: use `on()` for firing every time, `one()` for firing only for the first-time.

###blockedUserClick
This event fires whenever a user clicks on eventList when calendar is locked. You can use this for your GUI - for example, unlock button triple-blink, change date button triple-blink etc.

```js
jQuery('#eventCalendarLimit').on('blockedUserClick', function(event)
 	{
	console.log("blockedUserClick event: A user clicked on a locked calendar.")
 	});
```

##JSON format
```json
[{startDate: "2016-04-25T11:15:00+0000", endDate: "2016-04-25T12:15:00+0000", id: 25, type: "available"},{startDate: "2016-04-25T15:15:00+0000", endDate: "2016-04-25T17:45:00+0000", id: 26, type: "full"}]
```

## Options
<table class="table table-bordered table-striped bs-events-table">
	<thead>
		<tr>
			<th>Option name</th>
			<th>Default value</th>
			<th>Values</th>
			<th>Description</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>pollingInterval</td>
			<td>0</td>
			<td>0-99999</td>
			<td>number of seconds; autorefresh JSON feed every `pollingInterval` seconds, `0` for never</td>
		</tr>
		<tr>
			<td>cacheJson</td>
			<td>true</td>
			<td>true | false</td>
			<td>if TRUE plugin get a json only first time and after plugin filter events  |  if FALSE plugin get a new json on each date change</td>
		</tr>
		<tr>
			<td>businessHours</td>
			<td>[false,false,false,false,false,false,false]</td>
			<td></td>
			<td>like '11:00-21:00,11:00-22:00,11:00-22:00,11:00-22:00,11:00-22:00,11:00-23:00,11:00-23:00' // sunday,monday,tuesday...,saturday</td>
		</tr>
		<tr>
			<td>timeFormat</td>
			<td>'H:mm'</td>
			<td>any moment.js format</td>
			<td></td>
		</tr>
		<tr>
			<td>dateFormat</td>
			<td>'D. MMMM'</td>
			<td>any moment.js format</td>
			<td></td>
		</tr>
		<tr>
			<td>dateRangeIntradayFormat</td>
			<td>['D. MMMM H:mm', 'H:mm']</td>
			<td>[firstPart, secondPart]</td>
			<td>any moment.js format</td>
		</tr>
		<tr>
			<td>dateRangeMultidayFormat</td>
			<td>['D.', 'D. MMMM']</td>
			<td>[firstPart, secondPart]</td>
			<td>any moment.js format</td>
		</tr>
		<tr>
			<td>injectMethod</td>
			<td>'prepend'</td>
			<td>prepend | append</td>
			<td>Not important if you are injecting calendar in an empty div, but useful when you are injecting in an existing non-blank div. Example: `jQuery(".myCalendars").eventCalendar(myOptions)` = init calendar on .myCalendar and... APPEND or PREPEND the calendar layout template to it? You decide.</td>
		</tr>
		<tr>
			<td>injectTo</td>
			<td></td>
			<td>'right_column_div'</td>
			<td>false = append to the main element which is calendar inited on<br>"string" = find a subtag for appending/prepending whole layout to. Example: ".column-2" = appends or prepends to column-2 class subelement</td>
		</tr>
		<tr>
			<td>startWeekOnMonday</td>
			<td>true</td>
			<td>true | false</td>
			<td></td>
		</tr>
		<tr>
			<td>showDayNameInCalendar</td>
			<td>true</td>
			<td>true | false</td>
			<td></td>
		</tr>
		<tr>
			<td>onLoadDatetime</td>
			<td>moment()</td>
			<td>any moment.js time</td>
			<td>It is always better to provide reference server time. If not provided, local machine time is used, but beware, local time can be off and wrong timezone can be set.</td>
		</tr>
		<tr>
			<td>onLoadEventListView</td>
			<td>upcoming</td>
			<td>upcoming | month | day</td>
			<td></td>
		</tr>
		<tr>
			<td>upcomingTimeLimit</td>
			<td>[1, "month"]</td>
			<td>[1, "month"], [4, "weeks"], [1, "year"]</td>
			<td></td>
		</tr>
		<tr>
			<td>upcomingFilterEventTypeTo</td>
			<td>false</td>
			<td>true | false</td>
			<td>Example: se to `available` to show only available slots in upcoming events list. Note: you have to provide the `type` property (`available | full`) in JSON from backend.</td>
		</tr>
		<tr>
			<td>upcomingCountLimit</td>
			<td>10</td>
			<td></td>
			<td>limit number of events in eventsList upcoming view</td>
		</tr>
		<tr>
			<td>eventsCountlimit</td>
			<td>0</td>
			<td></td>
			<td>limit number of events in eventsList day/month view</td>
		</tr>
		<tr>
			<td>allowUpcomingView</td>
			<td>true</td>
			<td>true | false</td>
			<td></td>
		</tr>
		<tr>
			<td>allowMonthSummaryView</td>
			<td>true</td>
			<td>true | false</td>
			<td></td>
		</tr>
		<tr>
			<td>showDate</td>
			<td>true</td>
			<td>true | false</td>
			<td></td>
		</tr>
		<tr>
			<td>showTime</td>
			<td>true</td>
			<td>true | false</td>
			<td></td>
		</tr>
		<tr>
			<td>showRange</td>
			<td>"multidayEventsOnly"</td>
			<td>never | whenPossible | multidayEventsOnly</td>
			<td>"whenPossible" = shows event datetime as datetime range for all events >=1 second long. Don't forget to ###send endTime value in JSON.<br>"never" = shows only event start datetimes.<br>"multidayEventsOnly" - show range for multiday events only; show only the start time for intraday events. (DEFAULT)</td>
		</tr>
		<tr>
			<td>showDescription</td>
			<td>false</td>
			<td>true | false</td>
			<td></td>
		</tr>
		<tr>
			<td>openEventInNewWindow</td>
			<td>false</td>
			<td>true | false</td>
			<td></td>
		</tr>
		<tr>
			<td>limitView</td>
			<td>false</td>
			<td>true | false</td>
			<td>true = user can only navigate in specific range of months</td>
		</tr>
		<tr>
			<td>limitViewAutoMode</td>
			<td>true</td>
			<td>true | false</td>
			<td>automatic limit: first event..last event</td>
		</tr>
		<tr>
			<td>limitViewStartdate</td>
			<td>false</td>
			<td>moment object | false</td>
			<td></td>
		</tr>
		<tr>
			<td>limitViewEnddate</td>
			<td>false</td>
			<td>moment object | false</td>
			<td></td>
		</tr>
		<tr>
			<td>animationSlider</td>
			<td>300</td>
			<td></td>
			<td>milliseconds</td>
		</tr>
		<tr>
			<td>animationList</td>
			<td>300</td>
			<td></td>
			<td>milliseconds</td>
		</tr>
		<tr>
			<td>moveOpacity</td>
			<td>0.1</td>
			<td></td>
			<td>month and events fadeOut to this opacity</td>
		</tr>
		<tr>
			<td>allowEventSelection</td>
			<td>false</td>
			<td>true | false</td>
			<td>false for passive event listing, true for interactive apps</td>
		</tr>
		<tr>
			<td>lang</td>
			<td>object</td>
			<td></td>
			<td>see lang format below</td>
		</tr>
	</tbody>
</table>	

### lang
Easily translate to your language.

```js
pecLolaleCzech =
	{
	subtitleSelectedDay: '{0}',
	nextMonth:           'další měsíc',
	prevMonth:           'předchozí měsíc',
	nextEvents:          'nejbližší volné termíny:',
	viewMore:            'podrobnosti »',
	networkError:        'Chyba při načítání dat.',
	jsonFormatError:     'Chyba při načítání dat.',
	today:               'dnes',
	tomorrow:            'zítra',
	dayAfterTomorrow:    'pozítří',
	yesterday:           'včera',
	closed:              '',
	noEventsThisDay:     'termíny pro tento den nevypsány',
	noEventsThisMonth:   'termíny pro tento měsíc nevypsány',
	noUpcomingEvents:    'žádné volné termíny nebyly vypsány',
	withEvents:          '',
	lastItemText:        '', //V ostatní dny se na Vás těšíme ve standardní otevírací dobu.',
	businessHoursText:   '', //Těšíme se na vás v {0} hod!
	monthNames:          [ 'leden', 'únor', 'březen', 'duben', 'květen', 'červen', 'červenec', 'srpen', 'září', 'říjen', 'listopad', 'prosinec' ],
	dayNames:            [ 'pondělí', 'úterý', 'středa', 'čtvrtek', 'pátek', 'sobota', 'neděle'],
	nextDayNames:        [ 'toto pondělí', 'toto úterý', 'tuto středu', 'tento čtvrtek', 'tento pátek', 'tuto sobotu', 'tuto neděli'],
	dayNamesShort:       [ 'Ne', 'Po', 'Út', 'St', 'Čt', 'Pá', 'So' ]
	}

jQuery.fn.eventCalendar.defaults.lang = pecLolaleCzech
```

### All the options

Here are all the options and their defaults

```js
options =
	{
    jsonData:              null,   // JSON string or feed URL
	pollingInterval:       0,      // autorefresh JSON feed [number of seconds]
	cacheJson:             true,   // if TRUE plugin get a json only first time and after plugin filter events  |  if FALSE plugin get a new json on each date change
	businessHours:         [false,false,false,false,false,false,false], // like '11:00-21:00,11:00-22:00,11:00-22:00,11:00-22:00,11:00-22:00,11:00-23:00,11:00-23:00' // sunday,monday,tuesday...,saturday
	timeFormat:           'H:mm',
	dateFormat:           'D. MMMM',
	dateRangeIntradayFormat:  ['D. MMMM H:mm', 'H:mm'],
	dateRangeMultidayFormat:  ['D.', 'D. MMMM'],
	injectMethod:         'prepend', // Not important if you are injecting calendar in an empty div, but useful when you are injecting in an existing non-blank div. Example: jQuery(".myCalendars").eventCalendar(myOptions) = init calendar on .myCalendar and... APPEND or PREPEND the calendar layout template to it? You decide.
	injectTo:             'right_column_div',   // false = append to the main element which is calendar inited on   |   "string" = find a subtag for appending/prepending whole layout to. Example: ".column-2" = appends or prepends to column-2 class subelement
	startWeekOnMonday:     true,
	showDayNameInCalendar: true,
	onLoadDatetime:        moment(),
	onLoadEventListView:   "upcoming",
	upcomingTimeLimit:     [1, "month"], // example: [1, "month"], [4, "weeks"], [1, "year"]
	upcomingFilterEventTypeTo: false,
	upcomingCountLimit:    10,
	eventsCountlimit:      0,
	allowUpcomingView:     true,
	allowMonthSummaryView: true,
	showDate:              true,
	showTime:              true,
	showRange:             "multidayEventsOnly", // never | whenPossible | multidayEventsOnly, "whenPossible" = shows event datetime as datetime range for all events >=1 second long. Don't forget to send endTime value in JSON. "never" = shows only event start datetimes. "multidayEventsOnly" - show range for multiday events only; show only the start time for intraday events. (DEFAULT)
	showDescription:       false,
	onlyOneDescription:    true,
	openEventInNewWindow:  false,
	limitView:             false, // true = user can only navigate in specific range of months
	limitViewAutoMode:     true,  // automatic limit: first event..last event
	limitViewStartdate:    false,
	limitViewEnddate:      false,
	animationSlider:       300,
	animationList:         300,
	moveOpacity:           0.1, // month and events fadeOut to this opacity
	allowEventSelection:   false,
	lang:
		{
		subtitleSelectedDay: '{0}',
		nextMonth:           'next month',
		prevMonth:           'previous month',
		nextEvents:          'next events:',
		viewMore:            'more »',
		networkError:        'Network error.',
		jsonFormatError:     'Data error.',
		today:               'today',
		tomorrow:            'tomorrow',
		dayAfterTomorrow:    'day after tomorrow',
		yesterday:           'yesterday',
		closed:              '',
		noEventsThisDay:     'no events to show for this day',
		noEventsThisMonth:   'no events to show for this month',
		noUpcomingEvents:    'no upcoming events',
		withEvents:          '',
		lastItemText:        '', //V ostatní dny se na Vás těšíme ve standardní otevírací dobu.',
		businessHoursText:   'We are ', //Těšíme se na vás v {0} hod!
		monthNames:            [ "January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December" ],
		dayNames:              [ 'Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday' ],
		nextDayNames:          [ 'next Sunday', 'next Monday', 'next Tuesday','next Wednesday', 'next Thursday', 'next Friday', 'next Saturday' ],
		dayNamesShort:         [ 'Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat' ],
		},

	switchedViewCallback:              function(whatChanged, datetime) { },
	clickedOnEventInEventListCallback: function(event) { },
	smartEventDescription:             function(event, eventIsHighlighted) { return event.description; },
	};
```

##Override default options
Overriding default options for every new instance is easy, example:
```js
jQuery.fn.eventCalendar.defaults.cacheJson = false
```

##Source code
plutanium-event-calendar.js is a fully functional stable closed-source jquery library.

Do you want the calendar for you website? Let me know! I can also implement any custom functionality for you.

(c) 2016 babca