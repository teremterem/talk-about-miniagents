gpt-4o-2024-05-13
-------------------------------
# MiniAgents

MiniAgents is a Python framework designed to facilitate the creation and management of asynchronous agents that can interact with each other and external systems, such as language models (LLMs). The framework provides a robust and flexible architecture for building complex, asynchronous workflows with ease.

## Features

- **Asynchronous Agents**: Create agents that can run in parallel and interact with each other asynchronously.
- **Language Model Integration**: Seamlessly integrate with popular language models like OpenAI and Anthropic.
- **Message Handling**: Define and manage messages between agents with support for nested messages and serialization.
- **Promises and Streams**: Utilize promises and streams to handle asynchronous data flows efficiently.
- **Event Handling**: Customize behavior with event handlers for message persistence and node collection.

## Installation

To install MiniAgents, you can use [Poetry](https://python-poetry.org/):

```sh
poetry add miniagents
```

## Quick Start

Here's a quick example to get you started with MiniAgents:

### Example: Using OpenAI Agent

```python
import asyncio
from dotenv import load_dotenv
from miniagents.messages import Message
from miniagents.miniagents import MiniAgents
from miniagents.ext.llm.openai import create_openai_agent

load_dotenv()

llm_agent = create_openai_agent(model="gpt-4o-2024-05-13")
mini_agents = MiniAgents()

@mini_agents.on_persist_message
async def persist_message(_, message: Message) -> None:
    print("HASH KEY:", message.hash_key)
    print(type(message).__name__)
    pprint(message.serialize(), width=119)
    print()

async def main() -> None:
    async with mini_agents:
        reply_sequence = llm_agent.inquire(
            "How are you today?",
            max_tokens=1000,
            temperature=0.0,
            system="Respond only in Yoda-speak.",
        )

        async for msg_promise in reply_sequence:
            async for token in msg_promise:
                print(token, end="", flush=True)
            print()

if __name__ == "__main__":
    asyncio.run(main())
```

## Documentation

### Core Concepts

#### MiniAgents

The `MiniAgents` class is the core of the framework. It manages the lifecycle of agents and provides context for asynchronous operations.

```python
from miniagents.miniagents import MiniAgents

mini_agents = MiniAgents()
```

#### Agents

Agents are defined using the `@miniagent` decorator. They can perform asynchronous tasks and interact with other agents.

```python
from miniagents.miniagents import miniagent, InteractionContext

@miniagent
async def my_agent(ctx: InteractionContext) -> None:
    ctx.reply("Hello from my_agent!")
```

#### Messages

Messages are the primary means of communication between agents. They can be simple text messages or complex nested structures.

```python
from miniagents.messages import Message

message = Message(text="Hello, world!")
```

#### Promises

Promises are used to handle asynchronous data flows. They can be streamed piece by piece and collected as a whole.

```python
from miniagents.promising.promising import StreamedPromise

async def producer(_):
    yield "Hello"
    yield " "
    yield "world!"

promise = StreamedPromise(producer=producer)
```

### Integrations

#### OpenAI

To create an agent that interacts with OpenAI models:

```python
from miniagents.ext.llm.openai import create_openai_agent

llm_agent = create_openai_agent(model="gpt-4o-2024-05-13")
```

#### Anthropic

To create an agent that interacts with Anthropic models:

```python
from miniagents.ext.llm.anthropic import create_anthropic_agent

llm_agent = create_anthropic_agent(model="claude-3-haiku-20240307")
```

## Testing

MiniAgents comes with a comprehensive test suite. To run the tests, use `pytest`:

```sh
pytest
```

## Contributing

Contributions are welcome! Please read the [contributing guidelines](CONTRIBUTING.md) for more information.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Acknowledgements

Special thanks to all contributors and the open-source community for their support and contributions.

---

For more detailed documentation and examples, please visit the [official repository](https://github.com/teremterem/MiniAgents).
