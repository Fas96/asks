.. asks documentation master file, created by sphinx on Thu Mar 16 00:10:45 2017.


asks
====

Contents:
_________

.. toctree::
   :maxdepth: 2

   overview-of-funcs-and-args
   a-look-at-sessions
   the-response-object
   idioms

What is asks?
_____________

asks is an async http lib that can best be described as an effort to bring the same level of usable abstraction that requests offers synchronous python, to asynchronous python programming. Ideal for api interactions, webscraping etc.

It is important to note that the code examples in this documentation are to showcase asks, and not curio. In real code, it would be benificial to use things like curio's taskgroups and other neat tools to manage your requests. Here's a link to curio's docs for refrence:

http://curio.rtfd.io/

(The most useful thing to know when dealing with asks is a BoundedSemaphore, `for which I've written an example with asks <http://asks.readthedocs.io/en/latest/idioms.html#sanely-making-many-requests-with-semaphores>`_ )

Installation:
_____________

asks requires `Python 3.6 <www.python.org>`_ .

The easiest way to install asks is to pip it::

    pip install asks

asks was built for use with `Curio <https://github.com/dabeaz/curio>`_.
Internally asks uses the excellent `h11 <https://github.com/njsmith/h11>`_.

A dirty little example:
_______________________

Here's how to grab a single request and print it's content::

    # single_get.py
    import asks
    import curio

    async def grabber(url):
        r = await asks.get(url)
        print(r.content)

    curio.run(grabber('https://example.com'))

    # Results in:
    # b'<!doctype html>\n<html>\n<head>\n    <title>Example Domain</title>\n\n

Making one request in an async program is a little weird, but not without its use. This sort of basic ``asks.get()`` would slot in quite nicely in a greater program that makes some calls here and there.


A far finer example:
____________________

Here's an example of making 1000 calls to an api and storing the results in a list. We'll use the ``HSession`` here because all of our requests are to the same top level domain!::

    # many_get.py
    # make a whole pile of api calls and store
    # their response objects in a list.
    # Using the homogeneous-session.

    import asks
    import curio

    path_list = ['a', 'list', 'of', '1000', 'paths']

    retrieved_responses = []

    s = asks.HSession('https://some-web-service.com',
                      connections=20)

    async def grabber(a_path):
        r = await s.get(path=a_path)
        retrieved_responses.append(r)

    async def main(path_list):
        for path in path_list:
            curio.spawn(grabber(path))

    curio.run(main(path_list))

Now we're talkin'.

A thousand requests running async at the drop of a hat, using clean burning connection pooling to play nicely with the target server.


Why asks?
_________

If you like async, but don't like the spaghetti-docs furture-laden many-looped asyncio lib, you'll probably love curio. If you wish you could marry curio and requests, you'll probably love asks.

Nice libs like aiohttp suffer the side effect of uglyness due to being specifically for asyncio. Inspired by requests and curio, I wanted to take that lovely ultra abstraction and apply it to an async http lib to eleviate some of the pain in dealing with async http.


Features
________

asks packs most if not all of the features requests does. The usual ``.json()`` ing of responses and such. You can take a more in depth look `here <https://asks.readthedocs.io/en/latest/overview-of-funcs-and-args.html>`_.

However, because asks is aimed at crunching large piles of requests it takes a different approach to sessions. Sessions in asks are the main focus. More detail can be found `here <https://asks.readthedocs.io/en/latest/a-look-at-sessions.html>`_

The Future
__________
As soon as `trio <https://github.com/python-trio/trio>`_ supports async file i/o, asks will be available for trio too :)

Contributing
____________

Contributions are very welcome :)

About
_____

asks was created by Mark Jameson

https://theelous3.net

Shoutout to the fine folks of `8banana <https://github.com/8Banana>`_ and co.
