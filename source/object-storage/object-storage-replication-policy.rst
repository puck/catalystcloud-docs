#################################
Object Storage Replication Policy
#################################

By default anything placed into object storage will have the default
replication policy applied to it. This policy ensures that each object has
three replicas, where one replica is held at each of the regions.

.. code-block:: bash

    $ swift capabilities | grep policies
    policies: [{'default': True, 'name': 'o1-mr-r3', 'aliases': 'o1-mr-r3'}, {'name': '1-test-o1-sr-r3', 'aliases': '1-test-o1-sr-r3'}]

    $ swift stat -v
                        StorageURL: https://object-storage.ostst.wgtn.cat-it.co.nz:443/v1/AUTH_0ef8ecaa78684c399d1d514b61698fda
                        Auth Token: gAAAAABdwJ5KkgWpKIHN_4xaFxkqPpvivOO2Qc4kavx832WC3GNws74icYXvzGUQy7eHxkSgbSpbPzj-j2PikiY6KmbwaqFdlStRSUXbmW0ZR6edoKzw8fDy7FXedR1kWR-j83HQfICzw802Z1zbnZw1Tho7F6vDVo5OEyQw6ORQTSINl6diBD4
                            Account: AUTH_0ef8ecaa78684c399d1d514b61698fda
                        Containers: 1
                            Objects: 10971
                            Bytes: 165574
    Containers in policy "o1-mr-r3": 1
    Objects in policy "o1-mr-r3": 10971
        Bytes in policy "o1-mr-r3": 165574
                Meta Temp-Url-Key: testkey
                            Server: nginx/1.16.0
                    Content-Type: text/plain; charset=utf-8
                        X-Timestamp: 1479086186.21844
                    Accept-Ranges: bytes
        X-Account-Project-Domain-Id: default
                        X-Trans-Id: txf3477777e4504a46a06e5-005dc09e4b

    $ export storageURL="https://object-storage.ostst.wgtn.cat-it.co.nz:443/v1/AUTH_0ef8ecaa78684c399d1d514b61698fda"
    $ export token="gAAAAABdwJ5KkgWpKIHN_4xaFxkqPpvivOO2Qc4kavx832WC3GNws74icYXvzGUQy7eHxkSgbSpbPzj-j2PikiY6KmbwaqFdlStRSUXbmW0ZR6edoKzw8fDy7FXedR1kWR-j83HQfICzw802Z1zbnZw1Tho7F6vDVo5OEyQw6ORQTSINl6diBD4"
    $ curl -i -X GET -H "X-Auth-Token: $token" $storageURL
    HTTP/1.1 200 OK
    Server: nginx/1.16.0
    Date: Mon, 04 Nov 2019 21:56:05 GMT
    Content-Type: text/plain; charset=utf-8
    Content-Length: 21
    X-Account-Storage-Policy-O1-Mr-R3-Bytes-Used: 165574
    X-Account-Storage-Policy-O1-Mr-R3-Object-Count: 10971
    X-Account-Object-Count: 10971
    X-Timestamp: 1479086186.21844
    X-Account-Meta-Temp-Url-Key: testkey
    X-Account-Storage-Policy-O1-Mr-R3-Container-Count: 1
    X-Account-Bytes-Used: 165574
    X-Account-Container-Count: 1
    Accept-Ranges: bytes
    x-account-project-domain-id: default
    X-Trans-Id: tx281bed6b23004d94bc368-005dc09e75

    dispersion_objects_0



mark's notes : http://vedavec.wgtn.cat-it.co.nz/train/swift/swaddpolicy.html


Swift stat (or similar) will only show data for policies in which you have
*already created* containers, so is not useful for displaying *available*
policies. However this can be done with 'swift capabilities' which provides
(a lot) of data about what Swift options there are including the defined
policies. Again the openstack client cannot do this (but again you can do it
via curl, url is like http://localhost:8080/info - does not need an auth token)

Storage policies
================

Storage policies determine the data replication and durability strategy that
Swift will use for the objects stored in a given container.

The following storage classes exist on the Catalyst Cloud:

Storage class	Failure-domain	Replicas
o1-mr-r3 (default)	Multi-region	3 (one in each region)
nz-wlg-2-o1-sr-r3	Single-region	3 (all in one region)
nz-por-1-o1-sr-r3	Single-region	3 (all in one region)
nz-hlz-1-o1-sr-r3 | Single-region	3 (all in one region)

The storage class of a container is not yet visible nor configurable via the
Horizon dashboard yet. Customers are required to use the APIs/CLI to create a
container with a policy different than the default o1-mr-r3 policy.

