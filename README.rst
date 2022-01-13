advanced PAC
------------
Adnaced proxy auto-config (PAC) script

.. code:: javascript

    var proxy_addr = ["SOCKS5 127.0.0.1:9050"]

    /* or you can select from an array randomly */

    /*
    var proxy_addr = [
        "SOCKS5 127.0.0.1:9050",
        "SOCKS5 127.0.0.2:9050",
        "SOCKS5 127.0.0.3:9050",
        "SOCKS5 127.0.0.4:9050"
    ]
    */


    var count = 0 /* keeps the number of requests */
    var hostCounter = {} /* keeps the number of requests to each host */

    var ignore_assistant = '127.0.0.1'

    /* list of URLs which we don't have to proxy*/
    var urlsToIgnore = ['https://example.com/path/to?a=1']


    /* list of hosts which we don't have to proxy */
    var hostsToIgnore = [
        'localhost', '127.0.0.1',

        '*.example.com', 'example.com'
    ]

    function FindProxyForURL(url, host) {

        /* You can see alert message on browser console. */
        alert(`requesting -> ${url}`)

        /* Adds a host to ignore list on the fly
         * Call the following URL on your browser to add a host to your ignore list:
         *      http://127.0.0.1/?host_to_ignore=*.example.com
         */
        if(host === ignore_assistant) {
            let m = url.match(/\?host_to_ignore=(.*?)$/)

            if(m.length == 2) {
                let _host = m[1]

                hostsToIgnore.push(_host)
                alert(`Added "${_host}" to ignore list!`)
            }
        }

        if(hostCounter[host] !== undefined)
            hostCounter[host]++
        else
            hostCounter[host] = 0

        if(hostCounter[host] < 16) {
            let _count = hostCounter[host]
            alert(`\n${count} ->\n${dnsResolve(host)}\t\t${host} (${_count})\n\n${url}\n`)
        }

        count++

        for (var u of urlsToIgnore)
            /* or you can say if (url.startsWith(u)) or validate using regex */
            if (url == u) {
              alert(`DIRECT REQUEST TO URL: ${url} (${u})`)
              return "DIRECT"
            }

        for (var h of hostsToIgnore)
            if (shExpMatch(host, h)) {
                alert(`DIRECT REQUEST TO HOST: ${host} (${h})`)
                return "DIRECT"
            }

        return proxy_addr[~~(Math.random() * proxy_addr.length)]

    }

    /* Welcome message that prints ignore assistant hostname on browser console */
    alert(`Proxy Auto-Config is loaded. Ignore assistant hostname is ${ignore_assistant}`)


You can also add this bookmark to your browser to ease ignore process:

.. code:: javascript

    javascript:(function(){var host = prompt('Ignore proxy for this host?', location.hostname);window.open(`https://127.0.0.1/?host_to_ignore=${host}`, '_blank')})()
