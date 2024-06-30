gpt-4o-2024-05-13
-------------------------------
# MiniAgents

MiniAgents is a Python framework designed to facilitate the creation and management of asynchronous agents that can interact with each other and external systems, such as language models (LLMs). The framework provides a robust and flexible architecture for building complex, asynchronous workflows with ease.

## Features

- **Asynchronous Agents**: Create and manage agents that can run asynchronously and interact with each other.
- **LLM Integration**: Seamlessly integrate with language models from OpenAI and Anthropic.
- **Message Handling**: Define and handle messages between agents with a powerful and flexible messaging system.
- **Promises and Streams**: Utilize promises and streams to handle asynchronous data flows efficiently.
- **Extensibility**: Easily extend the framework to support new types of agents and interactions.

## Installation

To install MiniAgents, you can use `poetry`:

```sh
poetry add miniagents
```

## Quick Start

Here's a quick example to get you started with MiniAgents. This example demonstrates how to create an agent that interacts with an OpenAI language model.

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

The `MiniAgents` class is the core of the framework. It manages the lifecycle of agents and provides context for their interactions.

#### Agents

Agents are defined using the `@miniagent` decorator. They are asynchronous functions that can interact with each other and external systems.

#### Messages

Messages are the primary means of communication between agents. They can be simple text messages or complex objects with nested structures.

#### Promises and Streams

The framework uses promises and streams to handle asynchronous data flows efficiently. This allows for complex workflows to be built in a clean and maintainable way.

### Creating Agents

To create an agent, use the `@miniagent` decorator:

```python
from miniagents.miniagents import miniagent, InteractionContext

@miniagent
async def my_agent(ctx: InteractionContext, **kwargs) -> None:
    ctx.reply("Hello from my_agent!")
```

### Integrating with LLMs

MiniAgents provides built-in support for integrating with language models from OpenAI and Anthropic.

#### OpenAI

To create an agent that interacts with an OpenAI model:

```python
from miniagents.ext.llm.openai import create_openai_agent

llm_agent = create_openai_agent(model="gpt-4o-2024-05-13")
```

#### Anthropic

To create an agent that interacts with an Anthropic model:

```python
from miniagents.ext.llm.anthropic import create_anthropic_agent

llm_agent = create_anthropic_agent(model="claude-3-haiku-20240307")
```

### Handling Messages

Messages can be handled using the `@mini_agents.on_persist_message` decorator:

```python
@mini_agents.on_persist_message
async def persist_message(_, message: Message) -> None:
    print("Received message:", message)
```

## Testing

MiniAgents includes a comprehensive test suite to ensure the reliability and correctness of the framework. To run the tests, use `pytest`:

```sh
pytest
```

## Contributing

Contributions are welcome! Please feel free to submit a pull request or open an issue on GitHub.

## License

This project is licensed under the MIT License. See the [LICENSE](../LICENSE) file for details.

## Acknowledgements

Special thanks to the contributors and the open-source community for their support and contributions.

---

For more detailed documentation and examples, please refer to the [official documentation](https://github.com/teremterem/MiniAgents).
