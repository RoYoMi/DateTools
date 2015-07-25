### DateTools
Builds a date object, complete with methods for manipulating dates and times, via a programmer-friendly interface. Dates are automatically parsed from a variety of formats. When parsing dates, any missing date information will derived from the information provided or will be gathered from A_Now.

Note, this set of functions may not behave properly for dates before the 1600's.

### Required

	Authotkey v1.1.22 +

### Install and Use: 

	#Include DateTools.ahk
	
	Today := New date
	Today.Format := "ddd, yyyy MM dd"
	MsgBox, % "Today is '" . Today.Format . "'"
	
	MsgBox, % "Four Score and Seven Years ago was '" . Today.Add({Score:-4, Years:-7}) . "'"

### Syntax

	MyDate := new Date(value|hash)	: creates a new date object
			value 			: optional, string or date object, defaults to A_Now; The provided value will be parsed and This.DateTime will be set to the yyyyMMddHHmmss format
						: The provided string can be in any of a variety of formats and will be parsed. Any missing date parts (year,month,day,hour,minute,second) will be collected from A_Now
			hash			: optional, hash containing the desired options	
			DateTime	: Date/time in one of the many formats, this will be parsed and will populate This.DateTime
				Till		: Date/time, optional, only used for calculating differences between dates
			Region		: String, optional, defaults to "American". This describes the region that will be used in the parsing engine for determining the dd/mm or mm/dd formats
		.Add(Multiplier) := hash	: Applies date math to This.DateTime. returns This.Format after date math is done
		.Add(<hash>, Multiplier) 	: Applies date math to This.DateTime. returns This.Format after date math is done
			hash			: Positive values will move the date forward and negative values will move the date backwards. 
						: hash can contain keys from the following list {years, months, days, hours, minutes, seconds, weeks, fortnights, quarters, decades, score, centuries, weekdays, weekends, NextWeekend, NextWeekday, Mondays, Tuesdays, Wednesdays, Thursdays, Fridays, Saturdays, Sundays}
						: the system will process increments in the following order: Days, Months, NextWeekday, NextWeekend, Weekdays, Weekends, Mondays, Tuesdays, Wednesdays, Thursdays, Fridays, Saturdays, Sundays
			Seconds		: {hours, minutes, seconds} increments are first converted to Seconds then added to the DateTime in one operation.
			Days		: {days, weeks, fortnights} increments are first converted to Days then added to the DateTime in one operation.
				Months		: {Months, Quarters, Years, Biennium, Triennium, Olympiad, Lustrum, Decades, Indiction, Score, Jubilee, Centuries} increments are first converted to months then added to the DateTime in one operation. 
				Months		: moves the date the given number of months. If the last day of the month is less than the desired day, then the routine will return the last day of the month. The routine will calculate this correctly for leap years.
			NextWeekday : moves to the first Week day, in a given direction, does nothing if the current day is a weekday. a positive value will move forward, a negative value will move backward
			NextWeekend : moves to the first Weekend day, in a given direction, does nothing if the current day is a weekend. a positive value will move forward, a negative value will move backward
						: 
	
						: the following types will move This.DateTime forward or backwards to the next day of a the matching type
						: if the value is "next" or 0, then we move forward to the next type and stop; if the value is "Previous" then we move backwards to the next type and stop.
						: these allow calculations like fourth Thursday, or first Saturday after the second Tuesday.
						: these day centric operations are handled after the Day and Month calculations are processed
			Weekdays	: moves the date the given number of weekdays, and will ignore weekend days
				Weekends	: moves the date the given number of weekend days, and will ignore weekdays
			Mondays, Tuesdays, Wednesdays, Thursdays, Fridays, Saturdays, Sundays
		Multiplier		: default "SameDirection"; the number of times this will be applied, positive numbers continue in the same direction, negative numbers will reverse direction. Also accepts "SameDirection" or "Reverse" as syntactic sugar for 1 or -1  respectively.
		.TimeSpan(DateTime) := Till		: Sets This.Till and This.DateTime, and Calculates the difference between This.Till and This.DateTime. 
		.TimeSpan(Till, DateTime)		: Calculates the difference between Till and DateTime. Does not set This.Till or This.DateTime
						: Returns a hash containing all various TimeSpan Elements, which can be pushed back into a This.Add(<hash>) as the hash to move the same amount of time.
		Till			: optional, string or date object, defaults to This.Till
			DateTime		: optional, string or date object, defaults to This.DateTime
		.DateTime			: returns the This.DateTime in yyyyMMddHHmmss format. See also This.Format
		.Till 				: returns the This.Till date value. If not set then defaults to A_Now
		.Till := DateTime	: sets the This.Till date value. Returns This.Till
			DateTime 		: string in yyyyMMddHHmmss format
		.JulianDate := DateTime	: returns the astronomical julian dates extrapolated from This.DateTime see also https://en.wikipedia.org/wiki/Julian_day for more details
	.JulianDates(DateTime)	: returns the astronomical julian dates extrapolated from This.DateTime see also https://en.wikipedia.org/wiki/Julian_day for more details 
							: these are experimental and not fully tested
		Type				: optional string, if provided then the requested julian date type will be returned, otherwise a hash of all available julian dates will be returned
	---------------------------------------------------------------------
							: The following date parts will:
							: Getting a property will retrieve the actual date part requested, this will be a zero padded value, i.e. If Month = 3 then This.Month will return "03"
							: Setting a property will cause This.DateTime to be recalculated, and will return This.Format. See examples.
		.Year		:= value	: Acceptable values need to be in the format yyyy or yy. If 2 digit years are provided then it's assumed to be in the range 1950-2049. Get will return a 4 digit year.
	.Month		:= value	: Acceptable values can be 1-12, where 1 is January or at least the first three characters of the month name; The change remains in the current Year.
							: Note: this uses the This.Add({Months:X}) method to deal with last day of month issues i.e. if Jan 31 is the current day, moving to Feb will force a leap year check for the year and will set the day to either 29 or 28 accordingly.
		.Day		:= value	: 
	.DayOfWeek 	:= value	: Acceptable values can be 1-7, where 1 is monday or at least the first three characters of the day name; The change remains in the current week.
	.DayOfYear 	:= value	: Acceptable values can be zero padded three digit number 001-366, and the date will be moved to this day of the year 
	.Hour		:= value	: Acceptable values can be 0-23, expressed in 24 hour format
	.Minute		:= value	: 
	.Second		:= value	: 
		.Meridian 	:= value	: "am" or "pm" value for This.DateTime, Setting will also adjust the This.Hour to the corresponding value
			value				: string, "am" or "pm"
		.MonthName			: gets the full Month name for This.DateTime
		.IsLeapYear			: gets a boolean value telling if This.DateTime is a leap year
		.IsAfter(Resolution) := DateTime		: returns boolean, true if This.DateTime IsAfter DateTime, False if This.DateTime is not After DateTime
		.IsAfter(DateTime, Resolution) 			: returns boolean, true if This.DateTime IsAfter DateTime, False if This.DateTime is not After DateTime
		DateTime		: required, string or DateTime object. This value will be parsed into the Resolution format automatically before comparison
			Resolution		: optional, format string, default "yyyyMMdd"; Each of the dates will reformatted as this for comparison. Note using formats with text names will cause spurious results.
		.IsBefore(Resolution) := DateTime		: returns boolean, true if This.DateTime IsBefore DateTime, False if This.DateTime is not Before DateTime
		.IsBefore(DateTime, Resolution) 		: returns boolean, true if This.DateTime IsBefore DateTime, False if This.DateTime is not Before DateTime
		DateTime		: required, string or DateTime object. This value will be parsed into the Resolution format automatically before comparison
			Resolution		: optional, format string, default "yyyyMMdd"; Each of the dates will reformatted as this for comparison. Note using formats with text names will cause spurious results.
		.IsEqual(Resolution) := DateTime		: returns boolean, true if This.DateTime IsEqual DateTime, False if This.DateTime is not equal DateTime
		.IsEqual(DateTime, Resolution) 			: returns boolean, true if This.DateTime IsEqual DateTime, False if This.DateTime is not equal DateTime
		DateTime		: required, string or DateTime object. This value will be parsed into the Resolution format automatically before comparison 
			Resolution		: optional, format string, default "yyyyMMdd"; Each of the dates will reformatted as this for comparison. Note using formats with text names will cause spurious results.
		.IsBetween(Resolution) := DateTime	: returns Boolean, true if This.DateTime is Before This.Till and On/After This.DateTime
		.IsBetween(DateTime, Resolution)	: returns Boolean, true if This.DateTime is Before This.Till and On/After This.DateTime
		DateTime		: required, string or DateTime object. This value will be parsed into the Resolution format automatically before comparison
			Resolution		: optional, format string, default "yyyyMMdd"; Each of the dates will reformatted as this for comparison. Note using formats with text names will cause spurious results.
	.Parse(Region) := DateTime	: parses the Datetime, returns the DatePart Hash of the given DateTime
		.Parse(DateTime, Region) 	: parses the Datetime, returns the DatePart hash of the given DateTime
			DateTime		: optional, string datetime, defaults to This.DateTime
			Region			: optional, string region name, defaults to This.Region
		.Region				: returns the currently set region, if not set then defaults to "American"
		.Region := Value	: sets the region, returns the currently set region
		.DesiredFormat := value 				: gets or sets the desired format, use .Format instead
	.Format(value)		: returns This.DateTime in the specified format, does not save the format to This.DesiredFormat
	.Format := value	: sets This.Format. Syntactic sugar for This.DesiredFormat. Returns This.Format 
		value			: case sensitive string, default yyyyMMdd-HHmmss; Format that will be used to display This.DateTime.  
			d			: Day of the month without leading zero (1 - 31)
			dd			: Day of the month with leading zero (01 – 31)
			ddd			: Abbreviated name for the day of the week (e.g. Mon) in the current user's language
			dddd		: 	Full name for the day of the week (e.g. Monday) in the current user's language
			M			: Month without leading zero (1 – 12)
			MM			: Month with leading zero (01 – 12)
			MMM			: Abbreviated month name (e.g. Jan) in the current user's language
			MMMM		: 	Full month name (e.g. January) in the current user's language
			y			: Year without century, without leading zero (0 – 99)
			yy			: Year without century, with leading zero (00 - 99)
			yyyy		: 	Year with century. For example: 2005
			gg			: Period/era string for the current user's locale (blank if none)
			h			: Hours without leading zero; 12-hour format (1 - 12)
			hh			: Hours with leading zero; 12-hour format (01 – 12)
			H			: Hours without leading zero; 24-hour format (0 - 23)
			HH			: Hours with leading zero; 24-hour format (00– 23)
			m			: Minutes without leading zero (0 – 59)
			mm			: Minutes with leading zero (00 – 59)
			s			: Seconds without leading zero (0 – 59)
			ss			: Seconds with leading zero (00 – 59)
			t			: Single character time marker, such as A or P (depends on locale)
			tt			: Multi-character time marker, such as AM or PM (depends on locale)
	Examples
	DateTools.SyntaxExamples()
