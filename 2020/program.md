---
permalink: /program/
redirect_from: "/2020/program"
layout: egsr-default
title: Program
year: 2020
---

{% if page.year != null %}
	{% assign year = page.year %}
{% else %}
	{% assign year = site.data.egsr.current-year %}
{% endif %}

<meta charset='utf-8' />

<link href='../scripts/fullCalendar/packages/core/main.css' rel='stylesheet' />
<link href='../scripts/fullCalendar/packages/daygrid/main.css' rel='stylesheet' />
<link href='../scripts/fullCalendar/packages/timegrid/main.css' rel='stylesheet' />

<script src='../scripts/fullCalendar/packages/core/main.js'></script>
<script src='../scripts/fullCalendar/packages/daygrid/main.js'></script>
<script src='../scripts/fullCalendar/packages/timegrid/main.js'></script>
<script src="../scripts/moment.min.js"></script>
<script src="../scripts/moment-timezone-with-data.min.js"></script>

<div id='intro'>For EGSR fully virtual edition, each session comes with a youtube live link alongside a Rocket Chat link, offering different possibilities to ask questions and interacts with the other attendees.</div>

<script>
	UTCminTime = 12;
	UTCmaxTime = 18;
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.open( "GET", "/calendar-data/", false );
    xmlHttp.send( null );
	
	programStr = '\[' + String(String(xmlHttp.responseText).split("<code>")[1]).split("</code>")[0] + ']';
	jsonProg = JSON.parse(programStr);	
	columnHeadFormat = { weekday: 'long', month: 'numeric', day: 'numeric', omitCommas: true };
	if (window.screen.availWidth < 800)
	{
	    columnHeadFormat = { weekday: 'short', month: 'numeric', day: 'numeric', omitCommas: true };
	}

    document.addEventListener('DOMContentLoaded', function() {
	var calendarEl = document.getElementById('calendar');
	var calendar = new FullCalendar.Calendar(calendarEl, {
	plugins: ["timeGrid"],
	header: false,
	height: 'auto',
	timeZone: 'local',
	events:jsonProg,
	columnHeaderFormat: columnHeadFormat,
	slotLabelFormat: {hour: '2-digit',  minute: '2-digit', omitZeroMinute:false, meridiem: false, hour12: false, timeZoneName:'short'},
    defaultView: 'timeGridFiveDay',
	allDaySlot: false,
    minTime: String(UTCminTime) + ":00:00",
    maxTime: String(UTCmaxTime) + ":00:00",
	slotDuration: "01:00:01",
	nowIndicator:true,
	validRange: {
    start: '2020-06-29',
    end: '2020-07-04'
	},
	views: {
		timeGridFiveDay: {
		    type: 'timeGrid',
		    duration: { days: 5 }
			}
	  }
});
	var time = new Date();
	var timeZoneOffset = Math.floor(time.getTimezoneOffset() / 60);
	localMinTime = Math.max(UTCminTime - timeZoneOffset, 0);
	localMaxTime = Math.min(UTCmaxTime - timeZoneOffset, 24);

	calendar.setOption("minTime", String(localMinTime) + ":00:00");
	calendar.setOption("maxTime", String(localMaxTime) + ":00:00");

	calendar.render();
  });
</script>

<!--
{% for event in site.events %}
{{event.title}} {{event.event_date}}<br/>
{% endfor %}
-->
<div id="timezone"></div>
<div id="calendar"></div>
<script>
    var timeZoneStr = moment.tz.guess();
	var time = new Date();
	var timeZoneOffset = time.getTimezoneOffset();
	document.getElementById("timezone").innerHTML = "<h3>The program is generated for the timezone: " +timeZoneStr + " (" + moment.tz.zone(timeZoneStr).abbr(timeZoneOffset) + ")</h3>";
</script>


<div id="program" class="row-xs-12">
{% assign sessions = site.session | sort: 'start' %}
{% assign allTalks = site.data.talks[year]  | sort: 'talk_id' %}

{% for mySession in sessions %}
	<div class="session-content" id="{{mySession.session_id}}" >
		
		<h3 style="overflow: auto;"> <a href="{{mySession.permalink}}" ><span style="float: left; margin-top: 10px; margin-right: 10px;">{{mySession.title}}</span></a>{% if mySession.youtube_url%}<a href="{{mySession.youtube_url}}" ><img src="/img/program/yt.jpg" height="40px" width="65px" style="float: left;"></a>{% endif %}{% if mySession.rocket_chat_url%}<a href="{{mySession.rocket_chat_url}}" ><img src="/img/program/rocket-chat.svg" height="40px" width="65px" style="float: left;"></a>{% endif %}<a href="#intro" ><img src="/img/program/back-to-top.jpg" height="40px" width="65px" style="float: right;"></a></h3>
		<h4 class="time">{{mySession.start}}</h4>
		{% if mySession.abstract %}
			<h5>{{mySession.authors}}</h5>
		{% endif %}

		<div class="session-talks" >
			{% for talk in allTalks %}
				{% if talk.session_id == mySession.session_id%}
					<hr width="40%">

					<div>

						<h4>{{talk.title}}</h4>
						<h5>{{talk.authors}}</h5>
						{% if talk.abstract%}

						 <button type="button" class="abstract">Abstract</button>
						<div class="abstract_content">
						  <p>{{talk.abstract}}</p>
						</div> 
						{% endif %}

					</div>
				{% endif %}
					
			{% endfor %}

		</div>
					
		{% if mySession.abstract %}
			<div style="overflow: auto; margin-bottom:10px;"><div style="float: left; width:30%;"> <img height="185" width= "185" src="{{mySession.picture}}"></div><div style="float: left; width:70%;">{{mySession.Bio}}</div></div>
			 <button type="button" class="abstract">Abstract</button>
			<div class="abstract_content">
			  <p>{{mySession.abstract}}</p>
			</div> 
		{% endif %}
			
			<hr width="75%">
	</div>

{% endfor %}




</div>

<script>
	function timeFormat(utcTime){
		var local_date= moment.utc(utcTime).local().format('dddd DD MMMM HH:mm');
		var timeZoneStr = moment.tz.guess();
		var time = new Date();
		var timeZoneOffset = time.getTimezoneOffset();
		return local_date +" " +timeZoneStr + " (" + moment.tz.zone(timeZoneStr).abbr(timeZoneOffset) + ")";
	}
	
	var elements = document.getElementsByClassName("time");

	for(var i=0; i<elements.length; i++) {
		elements[i].innerHTML = timeFormat(elements[i].innerHTML);
	}
</script>

<script>
	var coll = document.getElementsByClassName("abstract");
	var i;

	for (i = 0; i < coll.length; i++) {
	  coll[i].addEventListener("click", function() {
		this.classList.toggle("open_abstract");
		var content = this.nextElementSibling;
		if (content.style.display === "block") {
		  content.style.display = "none";
		} else {
		  content.style.display = "block";
		}
	  });
	}
</script>

	