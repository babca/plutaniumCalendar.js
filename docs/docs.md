# plutanium-event-calendar
Plutanium Event Calendar is an event calendar for restaurants, sidebars, calendar widgets, order forms with appointment picker, event list. Written in jquery, usable with angular. Data are served in JSON.
Currently only an API is open sourced. If you are interested let me know!

##Getting started

###Browser

```html
<script src="moment.min.js"></script>
<script src="twix.min.js"></script>
<script src="plutanium-event-calendar.js"></script>
```

##Features

###frontend features
 * seamless autorefresh
 * single moment, allday and multiday events support
 * 3


###developer features
 * written in jQuery
 * compatible with AngularJS
 * fully customizable eventList content and behavior
 * you can connect it to your app, or indirectly to any external service like Google Calendar

##Public methods

###refresh
Redownload JSON data and repaint calendar&eventlist.
```js
ecMainInstance.refresh()
```

###repaint
just html refresh, this does not redownload data
```js
ecMainInstance.repaint()
```

###switchView

```js
ecMainInstance.switchView(datetime, switchTo, forceRefresh)
```

###disableEventSelection

```js
ecMainInstance.disableEventSelection()
```

###enableEventSelection

```js
ecMainInstance.enableEventSelection()
```

###selectEvent
zvoli termin podle jeho ID, to same jak kdyz uzivatel na nej klikne. Potom muze uzivatel zas kliknout jinam. Pozor opravdu jej jen zvoli, a to i kdyz neni zrovna videt! Typicky chces jeste dodatecne skocit na to datum aby videt bylo, takze zavolas jeste `switchView('day', datetime), resp. switchView('day', moment(getSelectedEvent().startDate))`. Vraci true/false podle toho jestli se selection zdaril (false=termin neexistuje)

```js
ecMainInstance.selectEvent(id)
```

###getEvent

```js
ecMainInstance.getEvent()
```

###getEvent

```js
ecMainInstance.getSelectedEvent()
```

###getSelectedEvent

```js
ecMainInstance.getEventsStorage()
```

###removeEventSelection

```js
ecMainInstance.removeEventSelection()
```

###highlightEvent
oznaci termin jako jako objednany, uzivatel muze klikat jinam ale zvyrazneni zustava. Obvykle potom jeste zamces kalendar proti dalsimu klikani pres disableEventSelection().
datetime must be a moment() object

```js
ecMainInstance.highlightEvent(datetime, repaintViewNow, repaintEventListNow)
```

##Callbacks

###switchedViewCallback

```js
ecMainInstance.removeEventSelection()
```

###clickedOnEventInEventListCallback

```js
ecMainInstance.removeEventSelection()
```

###refreshCallback

```js
ecMainInstance.removeEventSelection()
```

###smartEventDescription

```js
ecMainInstance.removeEventSelection()


```


// switchedViewCallback
// clickedOnEventInEventListCallback
// refreshCallback
// smartEventDescription

##Handle events


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

###eventListGenerated
on() for every time, one() for one-time

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

###blockedUserClick

```js
jQuery('#eventCalendarLimit').on('blockedUserClick', function(event)
 	{
	console.log("blockedUserClick event: A user clicked on a locked calendar. You can use it for UI - for example, unlock button triple-blink, change date button triple-blink...")
 	});
```

##JSON format
```json
[{startDate: "2016-04-25T11:15:00+0000", endDate: "2016-04-25T12:15:00+0000", id: 25, type: "available"},{startDate: "2016-04-25T15:15:00+0000", endDate: "2016-04-25T17:45:00+0000", id: 26, type: "full"}]
```

##Object format

###event object
```js
ecMainInstance.getEvent(28)
```

```js
Object
	{
	startDate: "2016-04-24T13:45:00+0000",
	endDate: "2016-04-24T20:00:00+0000",
	id: 28,
	type: "available"
	}
```

###eventsStorage object
Array of `event` objects.
```js
ecMainInstance.getEventsStorage()
```

```js
[ Object, Object, Object, Object, Object, ... ]
```

### Translate

```js
pecCzechLanguageSet =
	{
	subtitleSelectedDay: '{0}',
	nextMonth:         'další měsíc',
	prevMonth:         'předchozí měsíc',
	nextEvents:        'nejbližší volné termíny:',
	viewMore:          'podrobnosti »',
	networkError:      'Chyba při načítání dat.',
	jsonFormatError:   'Chyba při načítání dat.',
	today:             'dnes',
	tomorrow:          'zítra',
	dayAfterTomorrow:  'pozítří',
	yesterday:         'včera',
	closed:            '',
	noEventsThisDay:   'termíny pro tento den nevypsány',
	noEventsThisMonth: 'termíny pro tento měsíc nevypsány',
	noUpcomingEvents:  'žádné volné termíny nebyly vypsány',
	withEvents:        '',
	lastItemText:      '', //V ostatní dny se na Vás těšíme ve standardní otevírací dobu.',
	businessHoursText: '', //Těšíme se na vás v {0} hod!
	monthNames:    [ 'leden', 'únor', 'březen', 'duben', 'květen', 'červen', 'červenec', 'srpen', 'září', 'říjen', 'listopad', 'prosinec' ],
	dayNames:      [ 'pondělí', 'úterý', 'středa', 'čtvrtek', 'pátek', 'sobota', 'neděle'],
	nextDayNames:  [ 'toto pondělí', 'toto úterý', 'tuto středu', 'tento čtvrtek', 'tento pátek', 'tuto sobotu', 'tuto neděli'],
	dayNamesShort: [ 'Ne', 'Po', 'Út', 'St', 'Čt', 'Pá', 'So' ],
	}
```

### All the options

Here are all the options and their defaults

```js
pecMainInstance = jQuery('#eventCalendarLimit').eventCalendar(
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
		monthNames:            [ "January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December" ],
		dayNames:              [ 'Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday' ],
		nextDayNames:          [ 'next Sunday', 'next Monday', 'next Tuesday','next Wednesday', 'next Thursday', 'next Friday', 'next Saturday' ],
		dayNamesShort:         [ 'Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat' ],
		businessHoursText:     ""
		},

	switchedViewCallback:              function() { },
	clickedOnEventInEventListCallback: function() { },
	refreshCallback:                   function() { },
	smartEventDescription:             function(event)
		{
		return event.description;
		},

	};
```

##Source code

plutanium-event-calendar.js is a fully functional stable closed-source library. We are currently considering to open sourcing it. If you are interested let me know.