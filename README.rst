========
Overview
========


A Python SDK for VALR cryptocurrency exchange APIs.


Installation
============

::

    git clone https://github.com/VALR-Tech/valr-python
    cd valr-python
    python setup.py install



Authentication
==============

Authenticating to VALR API private resources requires a valid API Key from the `VALR exchange <https://www.valr.com/>`_.


Synchronous REST API Client
===========================


To use the **synchronous** REST API client:

.. code-block:: python

    >>> from valr_python import Client
    >>> from valr_python.exceptions import IncompleteOrderWarning
    >>> from decimal import Decimal
    >>>
    >>> c = Client(api_key='api_key', api_secret='api_secret')
    >>> c.rate_limiting_support = True # honour HTTP 429 "Retry-After" header values
    >>> limit_order = {
    ...     "side": "SELL",
    ...     "quantity": Decimal('0.1'),
    ...     "price": Decimal('10000'),
    ...     "pair": "BTCZAR",
    ...     "post_only": True,
    ... }
    >>> try:
    ...    res = c.post_limit_order(**limit_order)
    ...    order_id = res['id']
    ...    print(order_id)
    ... except IncompleteOrderWarning as w:  # HTTP 202 Accepted handling for incomplete orders
    ...    order_id = w.data['id']
    ...    print(order_id)
    ... except Exception as e:
    ...    print(e)
    "558f5e0a-ffd1-46dd-8fae-763d93fa2f25"


Asynchronous REST API Client
============================

The **asynchronous** REST API client is still in development.  *Coming soon!*


WebSocket API Client
====================

To use the WebSocket API client:


.. code-block:: python

    >>> import asyncio
    >>> from typing import Dict
    >>> from pprint import pprint
    >>> from valr_python import WebSocketClient
    >>> from valr_python.enum import TradeEvent
    >>> from valr_python.enum import WebSocketType
    >>>
    >>> def pretty_hook(data: Dict):
    ...    pprint(data)
    >>>
    >>> c = WebSocketClient(api_key='api_key', api_secret='api_secret', currency_pairs=['BTCZAR'],
    ...                     ws_type=WebSocketType.TRADE.name,
    ...                     trade_subscriptions=[TradeEvent.MARKET_SUMMARY_UPDATE.name],
    ...                     hooks={TradeEvent.MARKET_SUMMARY_UPDATE.name: pretty_hook})
    >>> loop = asyncio.get_event_loop()
    >>> loop.run_until_complete(c.run())
    {'currencyPairSymbol': 'BTCZAR',
     'data': {'askPrice': '151601',
              'baseVolume': '314.7631144',
              'bidPrice': '151600',
              'changeFromPrevious': '2.14',
              'created': '2020-02-06T22:47:03.129Z',
              'currencyPairSymbol': 'BTCZAR',
              'highPrice': '152440',
              'lastTradedPrice': '151600',
              'lowPrice': '146765',
              'previousClosePrice': '148410',
              'quoteVolume': '47167382.04552981'},
     'type': 'MARKET_SUMMARY_UPDATE'}


This library leverages :code:`websockets` and :code:`asyncio` and is thus a coroutine-based API client.  Both of
VALR's **Account WebSocket connection** and **Trade WebSocket connection** API endpoints are included.  Furthermore,
the SDK fully supports VALR's subscription methods for both :code:`Account` and :code:`Trade` endpoints.
Please see the `VALR API documentation <https://docs.valr.com/>`_ for further information.

For each subscription, a hook must be provided to process the WS responses.  Failing to do so raises
a :code:`HookNotFoundError` exception.  For ease of use, several :code:`Enum` classes have been implemented
(as showcased above) for client instantiation and hook consumption of API responses. However, client input is
accepted in :code:`str` format.

Although not completely minimalistic, please note that the SDK is implemented as a thin client and parsing of API
streams response is left up to the application user.


Development
===========

To execute all tests run::

    tox

Note, to combine the coverage data from all the tox environments run:

.. list-table::
    :widths: 10 90
    :stub-columns: 1

    - - Windows
      - ::

            set PYTEST_ADDOPTS=--cov-append
            tox

    - - Other
      - ::

            PYTEST_ADDOPTS=--cov-append tox


Donate
======

If this library has helped you or if you would like to support future development, donations are most welcome:

==============  ==========================================
Cryptocurrency  Address
==============  ==========================================
 **BTC**        38c7QWggrB2HLUJZFmhAC2zh4t8C57c1ec
 **ETH**        0x01eD3b58a07c6d005281Db76e6c1AE2bfF2226AD
==============  ==========================================
