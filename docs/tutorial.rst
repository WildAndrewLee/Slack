.. toctree::
   :maxdepth: 2
   :caption: Contents:

Munchlax Tutorial
=================
Using Munchlax is fairly straightfoward. First you'll need to get a Slack token. Once
you have one you'll need to decide on a command prefix. A command prefix is basically something
that designates a Slack message as a possible command for your bot. For example, using ``~`` as
your prefix means all messages prefixed with ``~`` will be processed as a possible command.

For simplicity, let's use ``~`` as our bot prefix.

.. code-block:: python

    import os
    from munchlax.bot import Bot

    bot = Bot()
    bot.set_token(os.environ['SLACK_TOKEN'])
    bot.set_prefix('~')

Now we can add some bot commands. Bot commands are functions with decorators over them.

.. code-block:: python

    @bot.command()
    async def hello(message):
        await message.reply('hi!')

Bot commands use the same names as their methods by default. If you want to specify a
specific command for a method you can pass ``cmd`` to ``Bot#command``.

.. code-block:: python

    @bot.command(cmd='alternative')
    async def hello(message):
        await message.reply('hi!')

Bot command functions are passed ``Message`` objects. You can then interact with these
messages however you want. Remember that bot command functions should be async functions.

We can also specify message requirements that must pass before our command functions are run.
The following is a function that divides an even number by 2 and will only run if an even number
is passed.

.. code-block:: python

    async def is_even(x):
        arg = x.split(' ')[1]

        if not arg.isnumeric():
            return False

        return float(arg) % 2 == 0

    @bot.command(requires=[lambda message: is_even(message.text)])
    async def divide_even(message):
        """
        A command that responds to ``~divide_even 10.6``
        """
        number = float(message.text.split(' ')[1])
        await message.reply(str(number / 2))

Once you have some commands specified you can start your bot with ``bot#start``.

Complete Tutorial Code
----------------------

.. code-block:: python

    import os
    from munchlax.bot import Bot

    bot = Bot()
    bot.set_token(os.environ['SLACK_TOKEN'])
    bot.set_prefix('~')

    @bot.command()
    async def hello(message):
        await message.reply('hi!')

    async def is_even(x):
        arg = x.split(' ')[1]

        if not arg.isnumeric():
            return False

        return float(arg) % 2 == 0

    @bot.command(requires=[lambda message: is_even(message.text)])
    async def divide_even(message):
        """
        A command that responds to ``~divide_even 10.6``
        """
        number = float(message.text.split(' ')[1])
        await message.reply(str(number / 2))
        
    bot.start()