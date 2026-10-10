# Smart Intake Capstone Projects

A collection of LLM-powered intelligent intake systems demonstrating structured data extraction, validation, and conversation memory management using Claude AI and LangChain.

## Overview

This capstone project contains two main components:

1. **Smart Incident Intake System** - A notebook-based incident ticket extraction system for IT service desks
2. **Medical Appointment Booking Assistant** - A CLI-based conversational system for scheduling medical appointments

Both systems leverage Claude LLM for intelligent data extraction with Pydantic validation and conversation memory.

---

## Project Structure

```
smart-intake/
├── smart-intake.ipynb              # Main incident intake extraction notebook
├── mock_1/                           # Medical appointment booking system
│   ├── main.py                       # CLI entry point for appointment booking
│   ├── appointment_tool.py           # Appointment booking logic
│   ├── extractor.py                  # Data extraction from user input
│   ├── llm_client.py                 # Claude LLM client wrapper
│   ├── memory.py                     # Conversation memory management
│   ├── models.py                     # Pydantic data models
│   ├── prompts.py                    # System prompts and few-shot examples
│   ├── .env                          # Environment variables
│   └── test.txt                      # Test cases/examples
├── requirements.txt                  # Python dependencies
├── .env                              # API keys and configuration
└── output/                           # Generated output files
```

---

## Component 1: Smart Incident Intake (smart-intake.ipynb)

### Purpose
An educational notebook demonstrating how to build an IT service desk intake system that extracts structured incident information from user descriptions using Claude AI.

### Features

**1. LLM Integration**
- Uses Claude Sonnet 4.6 model via Anthropic API
- Integrates with LangChain for enhanced LLM capabilities
- Conversation memory using LangChain memory modules

**2. Data Validation with Pydantic**
The system validates incident records with the following fields:

```python
IncidentRecord(
    incident_type: Literal["network_issue", "application_failure", "database_issue", 
                           "security_incident", "email_issue", "hardware_failure", 
                           "access_problem", "other"]
    affected_service: Literal["vpn", "email", "sap", "database", "network", 
                              "active_directory", "cloud_platform", "other"]
    severity: Literal["low", "medium", "high", "critical"]
    priority: Literal["p4", "p3", "p2", "p1"]
    reported_team: str (validated to be team name, not individual name)
    business_impact: str (required, non-empty)
)
```

**3. Intelligent Extraction Tool**
- JSON schema-based tool definition for Claude to use
- Structured output extraction with validation
- Business logic rules enforced at model level

**4. System Prompt Engineering**
Key rules implemented:
- Severity determined by business impact, not emotional language
- Team names required (not individual names)
- Enum values carefully selected based on incident description
- "other" category used when confidence is low

### Usage

```python
# Load environment
load_dotenv()

# Initialize LLM
client = anthropic.Anthropic(api_key=os.getenv("ANTHROPIC_API_KEY"))
llm = ChatAnthropic(model="claude-sonnet-4-6", max_tokens=512)

# Define incident record model
class IncidentRecord(BaseModel):
    # ... field definitions with validators

# Use extraction tool to process user input
# Extract incident fields from user description
```

### Key Concepts Demonstrated

- **Tool Use in LLMs**: How to define structured schemas for LLM output
- **Pydantic Validation**: Custom validators for business rules
- **Prompt Engineering**: Crafting system prompts with clear rules
- **LangChain Integration**: Using LangChain memory and LLMChain

---

## Component 2: Medical Appointment Booking System (mock_1/)

### Purpose
A production-like CLI application demonstrating a multi-turn conversational system for medical appointment scheduling with intelligent data extraction and conversation memory.

### Architecture

**Core Modules:**

#### `main.py` - Application Entry Point
```python
def run_cli():
    """Multi-turn CLI loop for appointment booking"""
    # 1. Greet user
    # 2. On user input: update memory, extract appointment info
    # 3. If incomplete: ask clarifying questions
    # 4. If complete: book appointment
```

#### `memory.py` - Conversation Memory
- Stores user and assistant messages
- Maintains context across turns
- Retrieves conversation history for extraction

#### `models.py` - Pydantic Models
- Appointment data model with required fields:
  - `patient_name`: Patient's full name
  - `doctor_specialty`: Medical specialty (e.g., Dermatologist, Cardiologist)
  - `appointment_reason`: Reason for appointment
  - `preferred_date`: Appointment date
  - `preferred_time`: Appointment time

#### `llm_client.py` - LLM Integration
- Wrapper around Claude API
- Handles API calls and response parsing
- Error handling and retry logic

#### `extractor.py` - Data Extraction
```python
def extract_appointment_info(user_input, conversation_history, llm):
    """
    Extract appointment fields from user input
    Returns: ExtractionResult with appointment data and missing fields
    """
```

#### `appointment_tool.py` - Booking Logic
```python
def book_appointment(appointment_info):
    """
    Confirm and book the appointment
    Returns: Booking status/confirmation message
    """
```

#### `prompts.py` - Prompt Templates
```python
SYSTEM_PROMPT = """
You are a medical appointment assistant.
- Collect: patient_name, doctor_specialty, appointment_reason, 
           preferred_date, preferred_time
- Never provide medical advice or diagnosis
- Strictly stay within appointment scheduling scope
- Ask for missing details when needed
"""

FEW_SHOT_EXAMPLES = [
    # Complete example
    {
        "user_input": "I'm Bob need a medical appointment on dermatology...",
        "assistant_output": {...},
        "missing_fields": []
    },
    # Incomplete example
    {
        "user_input": "I'm Bob and I need a medical appointment for my skin problem.",
        "assistant_output": {...},
        "missing_fields": ["preferred_date", "preferred_time"]
    }
]
```

### Features

1. **Multi-turn Conversation**
   - Maintains context across multiple user inputs
   - Asks clarifying questions for missing information
   - Confirms complete information before booking

2. **Intelligent Extraction**
   - Extracts 5 required appointment fields
   - Identifies missing fields
   - Generates contextual clarifying questions

3. **Conversation Memory**
   - Stores message history
   - Provides context to LLM for better extraction
   - Enables natural multi-turn interactions

4. **Safety Features**
   - Only collects appointment scheduling information
   - Refuses medical advice requests
   - Validates all required fields before booking

### Usage

```bash
# Run the appointment booking CLI
cd mock_1/
python main.py

# Interact with the chatbot
Welcome, How can I help you?
You: I'm John and I want to book a dermatology appointment for acne
Assistant: Thank you John. I have your information. What date would you prefer?
You: Next Tuesday at 2 PM
Assistant: Perfect! Let me book your appointment...
```

---

## Installation & Setup

### Prerequisites
- Python 3.9+
- Anthropic API key (Claude access)

### Install Dependencies

```bash
pip install -r requirements.txt
```

### Required Packages

- `anthropic` - Anthropic API client
- `langchain` - LLM orchestration framework
- `langchain-core` - Core LangChain components
- `langchain-anthropic` - Claude integration
- `pydantic` - Data validation
- `python-dotenv` - Environment variable management

### Environment Configuration

Create a `.env` file in the project root:

```env
ANTHROPIC_API_KEY=your_anthropic_api_key_here
```

Or in `mock_1/.env`:

```env
ANTHROPIC_API_KEY=your_anthropic_api_key_here
```

---

## Running the Projects

### 1. Smart Incident Intake Notebook

```bash
# Open the notebook in Jupyter or VS Code
jupyter notebook smart-intake.ipynb
# or open directly in VS Code
```

Execute cells sequentially to see:
- LLM initialization
- Pydantic model definitions
- Tool schema creation
- System prompt engineering
- Example extractions with validation

### 2. Medical Appointment Booking

```bash
cd mock_1/
python main.py
```

Then interact with the CLI:
```
Welcome, How can I help you?
You: I need a doctor's appointment for my knee pain
Assistant: I'd be happy to help schedule your appointment. What is your name?
...
```

---

## Key Learning Objectives

### Concepts Covered

1. **LLM Tool Use**
   - Defining tool schemas
   - Function calling patterns
   - Structured output extraction

2. **Data Validation**
   - Pydantic BaseModel
   - Field validators
   - Custom validation logic
   - Enum constraints

3. **Conversation Memory**
   - Message history storage
   - Context retrieval
   - Multi-turn dialogue

4. **Prompt Engineering**
   - System prompts design
   - Few-shot examples
   - Role-based personas
   - Clear instruction sets

5. **LangChain Integration**
   - ChatAnthropic wrapper
   - ConversationBufferMemory
   - LLMChain for prompt-LLM-output pipeline

6. **Application Design**
   - CLI interfaces
   - Error handling
   - State management
   - User experience

---

## Project Outputs

### Output Directory

Generated files are saved to `output/`:
- Extracted incident records
- Validation reports
- Error logs
- Processing results

---

## Testing & Examples

### Test Cases (mock_1/test.txt)

Example conversation flows:
- Complete appointment with all details
- Incomplete information requiring follow-up
- Out-of-scope queries (medical advice requests)
- Clarification rounds

---

## Architecture Diagram

```
User Input
    ↓
[Memory] (Conversation History)
    ↓
[LLM Client] → Claude API
    ↓
[Extractor] → Pydantic Models
    ↓
[Validators]
    ├─ If Complete → [Booking Tool] → Confirmation
    └─ If Incomplete → [Prompt Generator] → Clarifying Question
```

---

## Common Challenges & Solutions

### 1. Enum Constraint Violations
**Problem**: LLM selects values outside defined enums
**Solution**: Include enum values in system prompt with examples

### 2. Hallucination of Team Names
**Problem**: LLM invents team names not in domain
**Solution**: Use Pydantic validators to enforce team name validation

### 3. Conversation Context Loss
**Problem**: LLM forgets previous details in multi-turn
**Solution**: Use ConversationBufferMemory to maintain full history

### 4. Incomplete Data Processing
**Problem**: System processes incomplete records
**Solution**: Require all fields or explicitly ask for missing ones

---

## Future Enhancements

1. **Database Integration**
   - Store incident/appointment records
   - Query historical data
   - Implement booking persistence

2. **Advanced Memory**
   - Conversation summarization
   - Long-term memory
   - User profile management

3. **Multi-Language Support**
   - Support multiple languages
   - Localized prompts
   - Translation integration

4. **Analytics & Reporting**
   - Extraction success metrics
   - Common missing fields analysis
   - User interaction patterns

5. **Production Hardening**
   - Rate limiting
   - Error recovery
   - Audit logging
   - Role-based access control

---

## Troubleshooting

### API Key Issues
```bash
# Verify API key is set
echo $ANTHROPIC_API_KEY
```

### Import Errors
```bash
# Reinstall dependencies
pip install --upgrade -r requirements.txt
```

### Memory Issues
- For large conversations, consider ConversationSummaryMemory
- Implement conversation pruning for long sessions

---

## References

- [Anthropic Claude API Docs](https://docs.anthropic.com)
- [LangChain Documentation](https://docs.langchain.com)
- [Pydantic Documentation](https://docs.pydantic.dev)
- [Tool Use Best Practices](https://docs.anthropic.com/en/docs/build-a-system-prompt-with-tools)

---

## License

Educational project - Frontier Engineer Capstone

## Contributors

Capstone Project Team - Python Trainings

---

## Questions?

Refer to the notebook cells and inline comments for detailed explanations of each concept.
