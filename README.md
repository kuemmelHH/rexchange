= RExchange

RExchange is a pure ruby wrapper for the Microsoft Exchange Server WebDAV API

== Things you should know

* Requires Ruby 1.8.4 (or later, for the extended WebDAV support in the net/http library)
* RExchange is cross-platform compatible, being written in pure Ruby
* Kiwi fruits are packed with vitamins

== Why should you use RExchange

* It makes interacting with Exchange simple
* It was written for a real application, and does real work reliably day in and day out

== Example

	# We pass our uri (pointing directly to a mailbox), a username, and a password to RExchange::open
	# to create a RExchange::Session. Notice that we escape the "\" in our username.
	RExchange::open('https://example.com/exchange/admin/', 'mydomain\\admin', 'secret') do |mailbox|
  
		# The block parameter ("mailbox" in this case) is actually the Session itself.
		# You can refer to folders by chaining them as method calls. "inbox" in this case
		# isn't a defined method for Session, but part of the DSL to refer to folder names.
		# Each folder name returns a RExchange::Folder. The folder is Enumerable, allowing
		# iteration over the items in the folder, depending on the DAV:content-class of
		# the folder.
		mailbox.inbox.each do |message|
    
			# The "message" block parameter is a RExchange::Message object. You have access to
			# several attributes of the message, including: href, from, to, message_id, date,
			# importance, has_attachment? and body.
			p message.subject
    
			# The RExchange::Message#move_to method moves the message to another folder, in this
			# case, an "archive" folder off of the inbox.
			message.move_to mailbox.inbox.archive
		end
  
		# Folder names are "normalized", replacing dashes and spaces with underscores,
		# squeezing out multiple underscores in a row, and downcasing the whole thing.
		# So a folder name such as "My Very-long Folder Name" would look like:
		mailbox.my_very_long_folder_name
  
		# The Enumerable mixin gives us an Folder#entries method in case we just want to
		# return an array.
		messages = mailbox.inbox.entries
	
		# Other methods, like Enumerable#map and Enumerable#sort_by let us manipulate
		# the Folder in fun ways:
		puts mailbox.contacts.sort_by { |contact| contact.first_name }.map do |contact|
			<<-CONTACT
				Name:    #{contact.first_name} #{contact.last_name}
				Email:   #{contact.email}
				Phone:   #{contact.phone}
			CONTACT
		end
	
	end

	# We can access the Global Address Book by accessing the "/public" folder:
	RExchange::open('https://example.com/public', 'mydomain\\somebody', 'secret') do |mailbox|

		puts mailbox.company_contacts.each do |contact|
			<<-CONTACT
				Company: #{contact.company}
				Name:    #{contact.first_name} #{contact.last_name}
				Email:   #{contact.email}
				Phone:   #{contact.phone}
			CONTACT
	 end

	end
	
== Caveats

There are several features missing (simply because we didn't need them yet). Among them:

* The ability to delete messages or folders
* The ability to create folders
* There's no mechanism for sending new messages, or replying or forwarding existing ones
* There are a lot more message attributes we're not retrieving since they weren't useful to us, but they might be to you
* Exporting an email or entire folder tree to offline storage
* And much much more!

If you'd like to see any of these features, or have some ideas of your own you'd like to see implemented don't hesitate to let us know, and if it strikes our fancy maybe you'll get some free programming!
