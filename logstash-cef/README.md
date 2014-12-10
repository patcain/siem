LOGSTASH-CEF

Encased within this directory is a logstash filter to de-CEF events received from an Arcsight connector into their component pieces. I have an arcsight SEM system. Well, many of them. System and application events are sent from 
sytems and applications (duh) to an arcsight 'connector' which emits events in Arcsight's Common Event Format (CEF). 
CEF is really a series of key=value strings. Traditionally the events are then sent to an arcsight logger for saving, 
and possibly to an Arcsight ESM for other types of correlation or processing.

WHY?

Periodically we want to send the CEF-ed events to an elasticsearch instance via logstash to do special things. 
Just sending the CEF-ed events to logstash causes them to be indexed as one giant field which made retrieval
of small values difficult. Fine, we need to parse the cef into pieces. Initially this was going to be easy as logstash
already includes a key=value parser as a filter.  
But NOOO, cef is not really key=value delimited by spaces. It's key= value with some values embedding spaces.
This logstash filter is designed to handle all the odd cases where spaces show up in places where you would not 
expect them.  Equal signs (=), too, as they show up in values, too.
The reald driver for this filter was parsing juniper NSM logs, but we throw other devices at it tevery so often 
and fix any defieciencies discovered.

INSTALLATION

To install, copy the cef.rb file into your logstash filter directory and restart logstash. You logstash filter directory looks kind of like this: $LOGSTASH_HOME/lib/logstash/filters

CONFIGURATION

There is none if you like our defaults.; minimal choices otherwise.
The filter code contains a large table with three columns: 'CEF short code', 'CEF description', and 'useful'.  This 
leads to two useful things: First, after each key=values string is decoded the short code of the incoming event is replaced by the longer-easier-to-understand description from the table. You can edit the longer descriptions to fit your needs. Second, we found that many of the CEF fields were not of use but were large-ish. To let us cram as many events into our limited elasticsearch petrabyte database as possible we devised a scheme to toss CEF fields we 
didn't need. 'GeneratorId' for example. If the 'useful' column of the table for a CEF short code is true, then 
the decoded field - with the short code replaced by the description - is passed back to logstash for forwarding; if the useful column is "false" then the decoded key=value is tossed in the trash. Very useful for keys ans value you 
really don't want or are large and will impact your storage availability.

BUGS

There are none, but if you are pushy feel free to open a ticket/issue.

