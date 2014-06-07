## Data Travelling and Computing (DTC) Protocol ##

**Protocol v1.1**

Authors:


- Julien Amacher
- Pierre-Alain Curty
- Julien Bignens
- Bruno Brito Carvalho

v1.1:

- Julien Amacher
- Pierre-Alain Curty



### Messages format ###

Requests and responses are XML 1.0 encoded in UTF-8. Both request and response are terminated with the sequence r\n\r\n

Every request and response makes use of the “protocol” attribute which denotes the protocol’s version to use. Servers always answer with the same protocol the request used.

For now, since there is only one protocol, this attribute must be set to 1.0

<pre>
&lt;?xml version=&#8221;1.0&#8221; ? standalone=&#8221;yes&#8221;&gt;
&lt;response type=&quot;endpointsDiscovery&quot; protocol=&#8221;1.0&#8221;&gt;
    &lt;require_authentication&gt;true&lt;/require_authentication&gt;
&lt;/response&gt;
</pre>


### Transport protocol ###

Servers listen on TCP port 10871 for incoming connections in both ipv4 and ipv6 modes. 

### Timeout ###

Servers should close the connection when no data is received from the client within a determined time frame: t=2min.

### Beaconing ###

Servers send, at fixed intervals of 5 seconds, an ipv4 and ipv6 UDP broadcast beacon on port 10870 used to advertise itself on the network, which contents is as follows:

<pre>
&lt;?xml version=&#8221;1.0&#8221; standalone=&#8221;yes&#8221;&gt;
&lt;response type=&quot;endpointsDiscovery&quot; protocol=&#8221;1.0&#8221;&gt;
    &lt;require_authentication&gt;true&lt;/require_authentication&gt;
&lt;/response&gt;
</pre>

require_authentication: **true** if users should authenticate prior to using the services, **false** if no authentication is required.

Clients actively listening to these messages will then know the source address of this message is an endpoint itself.


### Registration ###

This request can only be issued if the server requires authentication.

#### Registration request : ####


<pre>
&lt;?xml version=&#8221;1.0&#8221; standalone=&#8221;yes&#8221; ?&gt;
&lt;request type=&#8221;registration&#8221; protocol=&#8221;1.0&#8221;&gt;
   &lt;nonce&gt;N8K1UUEQ&lt;/nonce&gt;
   &lt;user_id&gt;Luc El&#8217;Youssef&lt;/user_id&gt;
   &lt;email&gt;luc@luc.com&lt;/email&gt;
   &lt;password&gt;HEIG Fan&lt;/password&gt;
&lt;/request&gt;
</pre>

<table>
	<tr>
		<th>
			Field
		</th>
		<th>
			Description
		</th>
	</tr>
	<tr>
		<td>
			nonce
		</td>
		<td>
			A random and unique value allowing to correspond one request to its response
		</td>
	</tr>
	<tr>
		<td>
			user_id
		</td>
		<td>
			Identifier of the account to create Minimum length : 6 chars  Maximum length : 30 chars
		</td>
	</tr>
	<tr>
		<td>
			email
		</td>
		<td>
			Associated email address Must be a valid email address. Maximum length: 1500 chars
		</td>
	</tr>
	<tr>
		<td>
			password
		</td>
		<td>
			Password Minimum length : 6 chars
		</td>
	</tr>
</table>

#### Registration response : ####

<pre>
&lt;?xml version=&#8221;1.0&#8221; standalone=&#8221;yes&#8221; ?&gt;
&lt;response type=&#8221;registration&#8221; protocol=&#8221;1.0&#8221;&gt;&gt;
   &lt;nonce&gt;N8K1UUEQ&lt;/nonce&gt;
   &lt;result type=&#8221;success&#8221; /&gt;
&lt;/response&gt;
</pre>

In case something went wrong :

<pre>
&lt;?xml version=&#8221;1.0&#8221; standalone=&#8221;yes&#8221; ?&gt;
&lt;response type=&#8221;registration&#8221; protocol=&#8221;1.0&#8221;&gt;&gt;
   &lt;nonce&gt;N8K1UUEQ&lt;/nonce&gt;
   &lt;result type=&#8221;error&#8221;&gt;
       &lt;error&gt;
          &lt;code&gt;3&lt;/code&gt;
         &lt;description&gt;Email is a duplicate.&lt;/description&gt;
       &lt;/error&gt;
   &lt;/result&gt;
&lt;/response&gt;
</pre>

<table>
	<tr>
		<th>
			Code
		</th>
		<th>
			Description
		</th>
	</tr>
	<tr>
		<td>
			1
		</td>
		<td>
			Server does not require authentication
		</td>
	</tr>
	<tr>
		<td>
			2
		</td>
		<td>
			User id in invalid
		</td>
	</tr>
	<tr>
		<td>
			3
		</td>
		<td>
			User id is too long or too short
		</td>
	</tr>
	<tr>
		<td>
			4
		</td>
		<td>
			Password is too short
		</td>
	</tr>
	<tr>
		<td>
			5
		</td>
		<td>
			Password is invalid
		</td>
	</tr>
	<tr>
		<td>
			6
		</td>
		<td>
			User is is a duplicate
		</td>
	</tr>
	<tr>
		<td>
			7
		</td>
		<td>
			Internal error, cannot create the user account
		</td>
	</tr>
</table>

### Connect ###

#### With credentials : ####

<pre>
&lt;?xml version=&#8221;1.0&#8221; standalone=&#8221;yes&#8221; ?&gt;
&lt;request type=&#8221;connect&#8221; protocol=&#8221;1.0&#8221;&gt;&gt;
   &lt;credentials&gt;
      &lt;user_id&gt;Luc El&#8217;Youssef&lt;/user_id&gt;
      &lt;password&gt;HEIG Fan&lt;/password&gt;
   &lt;/ credentials&gt;
&lt;/request&gt;
</pre>

<table>
	<tr>
		<th>
			Field
		</th>
		<th>
			Description
		</th>
	</tr>
	<tr>
		<td>
			user_id
		</td>
		<td>
			Identifier of the account to login with
		</td>
	</tr>
	<tr>
		<td>
			password
		</td>
		<td>
			Associated account password
		</td>
	</tr>
</table>

#### Without credentials : ####

<pre>
&lt;?xml version=&#8221;1.0&#8221; standalone=&#8221;yes&#8221; ?&gt;
&lt;request type=&#8221;connect&#8221; protocol=&#8221;1.0&#8221;/&gt;
</pre>

### Connect response ###

<pre>
&lt;?xml version=&#8221;1.0&#8221; standalone=&#8221;yes&#8221; ?&gt;
&lt;response type=&#8221;connect&#8221; protocol=&#8221;1.0&#8221;&gt;
   &lt;result&gt;authenticated&lt;/result&gt;
&lt;/response&gt;
</pre>

<table>
	<tr>
		<th>
			Field
		</th>
		<th>
			Description
		</th>
	</tr>
	<tr>
		<td>
			result
		</td>
		<td>
			<strong>authenticated</strong> : issued when an authentication attempt is successful<br />
			<strong>connected</strong> : issued when a connection attempt is successful<br />
			<strong>authentication_error</strong> : issued when an authentication attempt is not successful<br />
			<strong>connection_error</strong> : issued when a connection attempt is not successful
		</td>
	</tr>
</table>

In case something went wrong :

<pre>
&lt;?xml version=&#8221;1.0&#8221; standalone=&#8221;yes&#8221; ?&gt;
&lt;response type=&#8221;connect&#8221; protocol=&#8221;1.0&#8221;&gt;
   &lt;result&gt;authentication_error&lt;/result&gt;
      &lt;error&gt;
          &lt;code&gt;4&lt;/code&gt;
         &lt;description&gt;The supplied user_id is not registered on this endpoint.&lt;/description&gt;
       &lt;/error&gt;
   &lt;/result&gt;
&lt;/response&gt;
</pre>

<table>
	<tr>
		<th>
			Code
		</th>
		<th>
			Description
		</th>
	</tr>
	<tr>
		<td>
			1
		</td>
		<td>
			User_id or password empty or missing
		</td>
	</tr>
	<tr>
		<td>
			2
		</td>
		<td>
			This server does not require authentication
		</td>
	</tr>
	<tr>
		<td>
			3
		</td>
		<td>
			Authentication is disabled (try again later)
		</td>
	</tr>
	<tr>
		<td>
			4
		</td>
		<td>
			User_id is not registered on the server
		</td>
	</tr>
	<tr>
		<td>
			5
		</td>
		<td>
			User_id is found but password is incorrect
		</td>
	</tr>
	<tr>
		<td>
			6
		</td>
		<td>
			Internal error
		</td>
	</tr>
</table>

Please note: connection to a server which has authentication disabled will always result in **authentication_error** in case credentials were supplied in the connection request.

### Listing services ###

This command can only be used once authenticated when the server requires authentication.

#### Listing services request : ####

<pre>
&lt;?xml version=&#8221;1.0&#8221; standalone=&#8221;yes&#8221; ?&gt;
&lt;request type=&#8221;listServices&#8221; protocol=&#8221;1.0&#8221; /&gt;
</pre>

#### Listing services response : ####

<pre>
&lt;?xml version=&#8221;1.0&#8221; standalone=&#8221;yes&#8221; ?&gt;
&lt;response type=&#8221;listServices&#8221; protocol=&#8221;1.0&#8221;&gt;
 &lt;result success=&#8221;true&#8221;&gt;
   &lt;services&gt;
      &lt;service&gt;
         &lt;name&gt;ADD&lt;/name&gt;
         &lt;description&gt;
         Adds two numeric values.
         &lt;/description&gt;
         &lt;parameters&gt;
            &lt;parameter order=&quot;1&quot;&gt;
               &lt;type&gt;numeric&lt;/type&gt;
               &lt;description&gt;
               The first value.
               &lt;/description&gt;
            &lt;/parameter&gt;
            &lt;parameter order=&quot;2&quot;&gt;
               &lt;type&gt;numeric&lt;/type&gt;
               &lt;description&gt;
               The second value.
               &lt;/description&gt;
            &lt;/parameter&gt;
          &lt;parameters&gt;
          &lt;returns&gt;
            &lt;type&gt;numeric&lt;/type&gt;
            &lt;description&gt;
            The sum of the two supplied values.
            &lt;/description&gt;
         &lt;/returns&gt;
      &lt;/service&gt;
      &lt;service&gt;
         &lt;name&gt;MIN&lt;/name&gt;
         &lt;description&gt;
         Returns the minimum value submitted.
         &lt;/description&gt;
         &lt;parameters&gt;
            &lt;parameter order=&quot;1&quot;&gt;
               &lt;type&gt;List[numeric]&lt;/type&gt;
               &lt;description&gt;
               List of numeric values.
               &lt;/description&gt;
            &lt;/parameter&gt;
          &lt;returns&gt;
            &lt;type&gt;numeric&lt;/type&gt;
            &lt;description&gt;
            The minimum value in the list.
            &lt;/description&gt;
         &lt;/returns&gt;
      &lt;/service&gt;
   &lt;/services&gt;
  &lt;/result&gt;
&lt;/response&gt;
</pre>

<table>
	<tr>
		<th>
			Field
		</th>
		<th>
			Description
		</th>
	</tr>
	<tr>
		<td>
			name
		</td>
		<td>
			Name of the service provided
		</td>
	</tr>
	<tr>
		<td>
			parameter > type
		</td>
		<td>
			Type of the parameter. Can be either of these primitives :<br /><br />

			- <strong>A numeric</strong> : any dot-separated or integer value<br />
			- <strong>An integer</strong> : any whole number<br />
			- <strong>A character</strong> : any character data<br />
			- <strong>A string</strong> : any sequence of character data<br /><br />
			
			A list of values is expected in case the type node is set to List[<strong>type</strong>]. In that case, <strong>type</strong> denotes any primitive type. Every value has to be separated from the preceded one with a space. In case of <strong>List[string]</strong>, every string has to be enclosed between two “. If the string contains “, these must be escaped with a \. Lists cannot be nested. 
		</td>
	</tr>
</table>

In case something went wrong :

<pre>
&lt;?xml version=&#8221;1.0&#8221; standalone=&#8221;yes&#8221; ?&gt;
&lt;response type=&#8221;listServices&#8221; protocol=&#8221;1.0&#8221;&gt;
   &lt;result success=&#8221;false&#8221;&gt;
       &lt;error&gt;
          &lt;code&gt;1&lt;/code&gt;
         &lt;description&gt;Authentication is required.&lt;/description&gt;
       &lt;/error&gt;
   &lt;/result&gt;
&lt;/response&gt;
</pre>

<table>
	<tr>
		<th>
			Code
		</th>
		<th>
			Description
		</th>
	</tr>
	<tr>
		<td>
			1
		</td>
		<td>
			Authentication is required
		</td>
	</tr>
</table>

### Consuming a service ###

This command can only be used once authenticated when the server requires authentication.

#### Consuming a service request ####

<pre>
&lt;?xml version=&#8221;1.0&#8221; standalone=&#8221;yes&#8221; ?&gt;
&lt;request type=&quot;consumeService&quot; protocol=&#8221;1.0&#8221;&gt;
   &lt;nonce&gt;3S1HVH8A&lt;/nonce&gt;
   &lt;name&gt;ADD&lt;/name&gt;
   &lt;parameters&gt;
      &lt;parameter order=&quot;1&quot;&gt;
         &lt;value&gt;5&lt;/value&gt;
      &lt;/parameter&gt;
      &lt;parameter order=&quot;2&quot;&gt;
         &lt;value&gt;7&lt;/value&gt;
      &lt;/parameter&gt;
   &lt;/parameters&gt;
&lt;/request&gt;
</pre>

<table>
	<tr>
		<th>
			Field
		</th>
		<th>
			Description
		</th>
	</tr>
	<tr>
		<td>
			nonce
		</td>
		<td>
			A random and unique value allowing to correspond one request to its response
		</td>
	</tr>
	<tr>
		<td>
			name
		</td>
		<td>
			The name of the service to consume
		</td>
	</tr>
	<tr>
		<td>
			parameter
		</td>
		<td>
			One value for each of the parameters
		</td>
	</tr>
<table>

#### Consuming a service response ####

<pre>
&lt;?xml version=&#8221;1.0&#8221; standalone=&#8221;yes&#8221; ?&gt;
&lt;response type=&quot;consumeService&quot; protocol=&#8221;1.0&#8221;&gt;
   &lt;nonce&gt;3S1HVH8A&lt;/nonce&gt;
   &lt;result success=&#8221;true&#8221;&gt;
       &lt;value&gt;12&lt;/value&gt;
   &lt;/result&gt;
&lt;/response&gt;
</pre>

<table>
	<tr>
		<th>
			Field
		</th>
		<th>
			Description
		</th>
	</tr>
	<tr>
		<td>
			nonce
		</td>
		<td>
			Same nonce which was supplied in the request
		</td>
	</tr>
	<tr>
		<td>
			result[success]
		</td>
		<td>
			<strong>true</strong> in case the calculation succeeded, <strong>false</strong> in case there was an error (see after)
		</td>
	</tr>
	<tr>
		<td>
			value
		</td>
		<td>
			Result of the requested calculation
		</td>
	</tr>
<table>

In case something went wrong :

<pre>
&lt;?xml version=&#8221;1.0&#8221; standalone=&#8221;yes&#8221;?&gt;
&lt;response type=&quot;consumeService&quot; protocol=&#8221;1.0&#8221;&gt;
   &lt;nonce&gt;3S1HVH8A&lt;/nonce&gt;
   &lt;result success=&#8221;false&#8221;&gt;
       &lt;error&gt;
          &lt;code&gt;4&lt;/code&gt;
         &lt;description&gt;Parameter 1 is empty.&lt;/description&gt;
       &lt;/error&gt;
   &lt;/result&gt;
&lt;/response&gt;
</pre>


<table>
	<tr>
		<th>
			Field
		</th>
		<th>
			Description
		</th>
	</tr>
	<tr>
		<td>
			1
		</td>
		<td>
			Authentication is required
		</td>
	</tr>
	<tr>
		<td>
			2
		</td>
		<td>
			The requested service does not exist (invalid name supplied)
		</td>
	</tr>
	<tr>
		<td>
			3
		</td>
		<td>
			The requested service is unavailable (try again later)
		</td>
	</tr>
	<tr>
		<td>
			4
		</td>
		<td>
			One or more mandatory argument is missing
		</td>
	</tr>
	<tr>
		<td>
			5
		</td>
		<td>
			Too many arguments provided
		</td>
	</tr>
	<tr>
		<td>
			6
		</td>
		<td>
			Invalid parameters type
		</td>
	</tr>
</table>