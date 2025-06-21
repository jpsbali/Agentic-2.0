# AI Travel Agent & Expense Planner Documentation

## Table of Contents
1. [Overview](#overview)
2. [System Architecture](#system-architecture)
3. [Installation & Setup](#installation--setup)
4. [Tool Categories](#tool-categories)
5. [Core Functions](#core-functions)
6. [Usage Examples](#usage-examples)
7. [API Reference](#api-reference)
8. [Configuration](#configuration)
9. [Troubleshooting](#troubleshooting)
10. [Best Practices](#best-practices)
11. [Advanced Features](#advanced-features)
12. [Conclusion](#conclusion)

---

## Overview

The **AI Travel Agent & Expense Planner** is a comprehensive travel planning system built with LangGraph and LangChain. It provides real-time travel information, cost calculations, weather forecasts, and detailed itineraries for any global destination.

### Key Features
- 🌍 **Global Coverage**: Plan trips to any city worldwide
- 🔄 **Real-time Data**: Live weather, exchange rates, and current information
- 💰 **Expense Planning**: Detailed cost breakdowns and budget calculations
- 🗓️ **Itinerary Generation**: Day-by-day travel plans
- 💱 **Currency Conversion**: Multi-currency support with live rates
- 🏨 **Budget-aware Planning**: Options for budget, mid-range, and luxury travel
- 🤖 **AI-Powered**: Intelligent tool orchestration using LangGraph

---

## System Architecture

### High-Level Architecture
```
User Input → LangGraph Workflow → Tool Selection → Data Gathering → Response Generation
```

### Component Overview
- **LLM**: Groq's DeepSeek R1 Distill Llama 70B
- **Framework**: LangGraph for workflow orchestration
- **Search Engine**: DuckDuckGo for real-time information
- **Tools**: 17 specialized tools across 6 categories

---

## Installation & Setup

### Prerequisites
```bash
Python 3.8+
pip or conda package manager
```

### Required Packages
```bash
pip install langchain-groq
pip install langgraph
pip install langchain-community
pip install requests
pip install duckduckgo-search
```

### Environment Setup
```python
# Set up Groq API key (if required)
import os
os.environ["GROQ_API_KEY"] = "your_api_key_here"
```

### Basic Initialization
```python
from langchain_groq import ChatGroq
from langgraph.graph import MessagesState, StateGraph
from langchain_community.tools import DuckDuckGoSearchRun

# Initialize LLM
llm = ChatGroq(
    model_name="deepseek-r1-distill-llama-70b",
    temperature=0.3
)

# Initialize search
search = DuckDuckGoSearchRun()
```

---

## Tool Categories

### A) Search Attractions and Activities (4 Tools)

#### 1. `search_attractions(destination_city, destination_country)`
**Purpose**: Find top attractions and landmarks
- **Input**: City and country names
- **Output**: List of popular attractions with descriptions
- **Example**: `search_attractions("Paris", "France")`

#### 2. `search_restaurants(destination_city, destination_country)`
**Purpose**: Discover local cuisine and dining options
- **Input**: City and country names
- **Output**: Restaurant recommendations and local food specialties
- **Example**: `search_restaurants("Tokyo", "Japan")`

#### 3. `search_activities(destination_city, destination_country)`
**Purpose**: Find activities and experiences
- **Input**: City and country names
- **Output**: Activities, tours, and experiences available
- **Example**: `search_activities("Dubai", "UAE")`

#### 4. `search_transportation(starting_city, starting_country, destination_city, destination_country)`
**Purpose**: Get transportation options and costs
- **Input**: Origin and destination details
- **Output**: Flight, train, bus options with pricing
- **Example**: `search_transportation("New York", "USA", "London", "UK")`

### B) Weather Forecasting (2 Tools)

#### 1. `get_current_weather(destination_city, destination_country)`
**Purpose**: Current weather conditions
- **Input**: City and country names
- **Output**: Temperature, conditions, humidity
- **Example**: `get_current_weather("Bangkok", "Thailand")`

#### 2. `get_weather_forecast(destination_city, destination_country, days=7)`
**Purpose**: Multi-day weather forecast
- **Input**: City, country, number of days
- **Output**: Extended weather forecast
- **Example**: `get_weather_forecast("Rome", "Italy", 5)`

### C) Hotel Cost Search (3 Tools)

#### 1. `search_hotels(destination_city, destination_country, budget_range="mid-range")`
**Purpose**: Find accommodation options
- **Input**: City, country, budget level
- **Output**: Hotel recommendations with pricing
- **Budget Options**: "budget", "mid-range", "luxury"
- **Example**: `search_hotels("Singapore", "Singapore", "luxury")`

#### 2. `estimate_hotel_cost(price_per_night, total_days)`
**Purpose**: Calculate total accommodation costs
- **Input**: Nightly rate, trip duration
- **Output**: Total hotel expense
- **Example**: `estimate_hotel_cost(150.0, 5)`

#### 3. `get_budget_range_info(budget_type, destination_city, destination_country)`
**Purpose**: Get budget range information
- **Input**: Budget type, city, country
- **Output**: Average costs for budget category
- **Example**: `get_budget_range_info("mid-range", "Barcelona", "Spain")`

### D) Cost Calculation (4 Tools)

#### 1. `add(a, b)`
**Purpose**: Basic addition
- **Input**: Two numbers
- **Output**: Sum
- **Example**: `add(150.0, 75.0)`

#### 2. `multiply(a, b)`
**Purpose**: Basic multiplication
- **Input**: Two numbers
- **Output**: Product
- **Example**: `multiply(120.0, 5)`

#### 3. `calculate_total_cost(hotel_cost, food_cost, transport_cost, activity_cost)`
**Purpose**: Calculate total trip expenses
- **Input**: All expense categories
- **Output**: Total trip cost
- **Example**: `calculate_total_cost(750.0, 300.0, 500.0, 200.0)`

#### 4. `calculate_daily_budget(total_cost, total_days)`
**Purpose**: Calculate daily spending budget
- **Input**: Total cost, trip duration
- **Output**: Daily budget
- **Example**: `calculate_daily_budget(1750.0, 7)`

### E) Currency Conversion (2 Tools)

#### 1. `get_exchange_rate(from_currency, to_currency)`
**Purpose**: Get current exchange rates
- **Input**: Source and target currency codes
- **Output**: Current exchange rate
- **Example**: `get_exchange_rate("USD", "EUR")`

#### 2. `convert_currency(amount, exchange_rate, from_currency, to_currency)`
**Purpose**: Convert currency amounts
- **Input**: Amount, rate, currency codes
- **Output**: Converted amount
- **Example**: `convert_currency(1000.0, 0.85, "USD", "EUR")`

### F) Itinerary Generation (2 Tools)

#### 1. `create_day_plan(destination_city, destination_country, day_number, total_days)`
**Purpose**: Plan specific days
- **Input**: City, country, day number, total days
- **Output**: Detailed day plan
- **Example**: `create_day_plan("Paris", "France", 3, 7)`

#### 2. `create_full_itinerary(destination_city, destination_country, total_days, interests="general")`
**Purpose**: Create complete itinerary
- **Input**: City, country, duration, interests
- **Output**: Full trip itinerary
- **Example**: `create_full_itinerary("Rome", "Italy", 5, "history and art")`

---

## Core Functions

### Main Travel Planning Function

```python
def plan_custom_trip(starting_city, starting_country, destination_city, destination_country, 
                    starting_currency, destination_currency, days, budget_range="mid-range", 
                    interests="general"):
    """
    Plan a custom trip with user-specified parameters.
    
    Args:
        starting_city (str): Origin city name
        starting_country (str): Origin country name
        destination_city (str): Destination city name
        destination_country (str): Destination country name
        starting_currency (str): Origin currency code (e.g., "USD")
        destination_currency (str): Destination currency code (e.g., "EUR")
        days (int): Trip duration in days
        budget_range (str): Budget level - "budget", "mid-range", or "luxury"
        interests (str): User interests and preferences
    
    Returns:
        dict: Complete travel plan with all components
    """
    query = f"""
    Plan a {days}-day comprehensive trip:
    - From: {starting_city}, {starting_country}
    - To: {destination_city}, {destination_country}
    - Starting Currency: {starting_currency}
    - Destination Currency: {destination_currency}
    - Budget Range: {budget_range}
    - Interests: {interests}
    
    Please provide:
    1. Current weather and forecast
    2. Top attractions and activities
    3. Restaurant recommendations
    4. Transportation options and costs
    5. Hotel recommendations and costs
    6. Currency conversion rates
    7. Day-by-day itinerary
    8. Total expense breakdown
    9. Daily budget calculation
    10. Trip summary
    """
    
    messages = [HumanMessage(content=query)]
    result = travel_agent_graph.invoke({"messages": messages})
    
    print(f"\n=== TRAVEL PLAN: {starting_city} to {destination_city} ({days} days) ===")
    for message in result['messages']:
        message.pretty_print()
    
    return result
```

### Travel Agent Graph Function

```python
def travel_agent_function(state: MessagesState):
    """
    Main workflow function for processing travel queries
    
    Args:
        state (MessagesState): Current state with user messages
    
    Returns:
        dict: Updated state with AI response
    """
    user_messages = state["messages"]
    input_messages = [SYSTEM_PROMPT] + user_messages
    response = llm_with_tools.invoke(input_messages)
    return {"messages": [response]}
```

### Utility Functions

#### Cost Analysis
```python
def analyze_trip_costs(trip_result):
    """
    Extract and analyze cost information from trip results
    
    Args:
        trip_result (dict): Complete trip planning result
    
    Returns:
        list: Extracted cost-related information
    """
    print("\n=== TRIP COST ANALYSIS ===")
    
    final_message = trip_result['messages'][-1]
    content = final_message.content if hasattr(final_message, 'content') else str(final_message)
    
    cost_keywords = ['cost', 'price', 'budget', 'expense', 'total', 'USD', 'EUR', 'GBP', 'INR', 'JPY']
    
    cost_lines = []
    for line in content.split('\n'):
        if any(keyword.lower() in line.lower() for keyword in cost_keywords):
            cost_lines.append(line.strip())
    
    if cost_lines:
        print("Cost-related information found:")
        for line in cost_lines[:10]:
            print(f"  - {line}")
    else:
        print("No specific cost information extracted from the response.")
    
    return cost_lines
```

#### Trip Summary
```python
def generate_trip_summary(starting_city, destination_city, days, budget_range):
    """
    Generate formatted trip summary
    
    Args:
        starting_city (str): Origin city
        destination_city (str): Destination city
        days (int): Trip duration
        budget_range (str): Budget level
    
    Returns:
        str: Formatted trip summary
    """
    summary = f"""
    ╔══════════════════════════════════════════════════════════════╗
    ║                        TRIP SUMMARY                          ║
    ╠══════════════════════════════════════════════════════════════╣
    ║  Route: {starting_city} → {destination_city:<30} ║
    ║  Duration: {days} days{' ' * (45 - len(str(days)))}║
    ║  Budget Level: {budget_range.title():<40} ║
    ║  Planning Date: {datetime.now().strftime('%Y-%m-%d'):<37} ║
    ╚══════════════════════════════════════════════════════════════╝
    """
    return summary
```

#### Quick Planner
```python
def quick_trip_planner():
    """
    Interactive trip planning interface
    
    Returns:
        dict: Complete trip planning result
    """
    print("\n🌍 AI TRAVEL AGENT & EXPENSE PLANNER 🌍")
    print("=====================================\n")
    
    # Demo configuration - customize as needed
    starting_city = "Delhi"
    starting_country = "India"
    destination_city = "Bangkok"
    destination_country = "Thailand"
    starting_currency = "INR"
    destination_currency = "THB"
    days = 6
    budget_range = "mid-range"
    interests = "temples, street food, and shopping"
    
    print(generate_trip_summary(starting_city, destination_city, days, budget_range))
    
    result = plan_custom_trip(
        starting_city, starting_country, 
        destination_city, destination_country,
        starting_currency, destination_currency,
        days, budget_range, interests
    )
    
    analyze_trip_costs(result)
    return result
```

---

## Usage Examples

### Basic Trip Planning

```python
# Simple trip planning
messages = [HumanMessage(content="""
Plan a 5-day trip:
- From: New York City, USA
- To: Paris, France
- Starting Currency: USD
- Destination Currency: EUR
- Budget Range: Mid-range
- Interests: Culture, food, and sightseeing

Please provide a complete travel plan with weather forecast, attractions, 
restaurants, activities, hotel costs, transportation options, currency 
conversion, daily itinerary, and total expense calculation.
""")]

result = travel_agent_graph.invoke({"messages": messages})
```

### Budget Trip Example

```python
# Budget-conscious planning
plan_custom_trip(
    "Mumbai", "India",
    "Goa", "India", 
    "INR", "INR",
    3, "budget",
    "beaches and local food"
)
```

### Luxury Business Trip

```python
# Business trip planning
plan_custom_trip(
    "San Francisco", "USA",
    "Singapore", "Singapore",
    "USD", "SGD", 
    4, "luxury",
    "business meetings and fine dining"
)
```

### Adventure Travel

```python
# Adventure-focused trip
plan_custom_trip(
    "Sydney", "Australia",
    "Queenstown", "New Zealand",
    "AUD", "NZD",
    7, "mid-range", 
    "adventure sports and nature"
)
```

### Cultural Exploration

```python
# Cultural immersion trip
plan_custom_trip(
    "London", "UK",
    "Tokyo", "Japan",
    "GBP", "JPY",
    10, "mid-range",
    "traditional culture, temples, and authentic experiences"
)
```

### Family Vacation

```python
# Family-friendly planning
plan_custom_trip(
    "Chicago", "USA",
    "Orlando", "USA",
    "USD", "USD",
    6, "mid-range",
    "family attractions, theme parks, and kid-friendly activities"
)
```

---

## API Reference

### System Messages

#### Travel Agent System Prompt
```python
SYSTEM_PROMPT = SystemMessage(
    content="""You are an AI Travel Agent & Expense Planner. Your role is to help users plan comprehensive trips with real-time data.
    
    You have access to tools for:
    - Searching attractions, restaurants, activities, and transportation
    - Getting weather information and forecasts
    - Finding hotels and calculating accommodation costs
    - Performing cost calculations and budget planning
    - Converting currencies
    - Creating detailed itineraries
    
    Always provide comprehensive travel plans including:
    1. Weather information
    2. Top attractions and activities
    3. Hotel cost calculations
    4. Currency conversion
    5. Complete itinerary
    6. Total expense breakdown
    7. Trip summary
    
    Be helpful, detailed, and always try to provide accurate, up-to-date information.
    """
)
```

### Graph Configuration

#### Workflow Setup
```python
# Build the workflow graph
workflow = StateGraph(MessagesState)

# Add nodes
workflow.add_node("travel_agent", travel_agent_function)
workflow.add_node("tools", ToolNode(tools))

# Add edges
workflow.add_edge(START, "travel_agent")
workflow.add_conditional_edges(
    "travel_agent",
    tools_condition,
)
workflow.add_edge("tools", "travel_agent")

# Compile the graph
travel_agent_graph = workflow.compile()
```

### Tools List
```python
tools = [
    # Attraction and Activity tools
    search_attractions, search_restaurants, search_activities, search_transportation,
    # Weather tools
    get_current_weather, get_weather_forecast,
    # Hotel tools
    search_hotels, estimate_hotel_cost, get_budget_range_info,
    # Calculation tools
    add, multiply, calculate_total_cost, calculate_daily_budget,
    # Currency tools
    get_exchange_rate, convert_currency,
    # Itinerary tools
    create_day_plan, create_full_itinerary
]
```

---

## Configuration

### LLM Configuration

```python
llm = ChatGroq(
    model_name="deepseek-r1-distill-llama-70b",
    temperature=0.3  # Adjust for creativity vs consistency
)
```

#### Temperature Settings:
- **0.0-0.3**: More deterministic, factual responses
- **0.4-0.7**: Balanced creativity and accuracy
- **0.8-1.0**: More creative, varied responses

### Search Configuration

```python
search = DuckDuckGoSearchRun(
    # Additional parameters can be configured here
)
```

### Budget Range Settings

| Budget Level | Accommodation | Dining | Activities |
|-------------|---------------|---------|------------|
| Budget | Hostels, Budget Hotels | Local eateries, Street food | Free attractions, Walking tours |
| Mid-range | 3-4 star hotels | Mix of local and international | Paid attractions, Some tours |
| Luxury | 5-star hotels, Resorts | Fine dining, Premium restaurants | Premium experiences, Private tours |

### Currency Codes Reference

#### Major Currencies
- **USD** - US Dollar
- **EUR** - Euro
- **GBP** - British Pound
- **JPY** - Japanese Yen
- **CNY** - Chinese Yuan
- **INR** - Indian Rupee
- **AUD** - Australian Dollar
- **CAD** - Canadian Dollar
- **CHF** - Swiss Franc
- **SGD** - Singapore Dollar

---

## Troubleshooting

### Common Issues

#### 1. Tool Not Found Error
**Problem**: `AttributeError: 'function' object has no attribute 'name'`

**Solution**: Ensure all tools are properly decorated with `@tool`
```python
@tool
def your_function_name():
    # function implementation
```

#### 2. Search Results Empty
**Problem**: No results returned from search tools

**Solutions**:
- Check internet connectivity
- Verify city/country names are spelled correctly
- Try alternative search terms
- Check if the destination exists

#### 3. Currency Conversion Issues
**Problem**: Exchange rate not found

**Solutions**:
- Use standard currency codes (USD, EUR, GBP, etc.)
- Check if the currency code is valid
- Retry after a few moments
- Verify both currencies are widely traded

#### 4. Graph Compilation Errors
**Problem**: Workflow fails to compile

**Solutions**:
- Verify all tools are in the tools list
- Check that all nodes are properly defined
- Ensure edges are correctly configured
- Validate tool decorators

#### 5. API Rate Limiting
**Problem**: Too many requests error

**Solutions**:
- Add delays between requests
- Implement retry logic
- Cache frequently used data
- Use batch processing where possible

### Debugging Tips

#### Enable Verbose Logging
```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

#### Test Individual Tools
```python
# Test a specific tool
result = search_attractions.invoke({
    "destination_city": "Paris", 
    "destination_country": "France"
})
print(result)
```

#### Validate Tool List
```python
# Check all tools are loaded
print(f"Total tools: {len(tools)}")
for tool in tools:
    print(f"- {tool.name}")
```

#### Memory Usage Monitoring
```python
import psutil
import os

def check_memory_usage():
    process = psutil.Process(os.getpid())
    memory_info = process.memory_info()
    print(f"Memory usage: {memory_info.rss / 1024 / 1024:.2f} MB")
```

---

## Best Practices

### Input Validation

#### City and Country Names
- Use full, official names: "New York City" not "NYC"
- Include country for clarity: "Paris, France" vs "Paris, Texas"
- Check spelling and capitalization
- Handle special characters properly

#### Currency Codes
- Use ISO 4217 standard codes (USD, EUR, GBP, JPY, etc.)
- Always use uppercase
- Verify currency is widely traded
- Handle deprecated currencies gracefully

#### Budget Ranges
- Stick to predefined options: "budget", "mid-range", "luxury"
- Consider destination cost of living
- Align expectations with budget level
- Provide clear budget guidelines

### Query Optimization

#### Effective Prompts
```python
# Good prompt structure
query = """
Plan a [duration]-day trip:
- From: [Starting City], [Starting Country]
- To: [Destination City], [Destination Country]
- Starting Currency: [Currency Code]
- Destination Currency: [Currency Code]
- Budget Range: [budget/mid-range/luxury]
- Interests: [Specific interests]

Please provide: [specific requirements]
"""
```

#### Interest Categories
- **Culture**: Museums, historical sites, art galleries, local traditions
- **Food**: Local cuisine, cooking classes, food tours, markets
- **Adventure**: Outdoor activities, sports, hiking, extreme sports
- **Relaxation**: Spas, beaches, quiet activities, wellness
- **Business**: Meeting venues, business districts, networking
- **Nightlife**: Bars, clubs, entertainment, live music
- **Family**: Kid-friendly activities, family attractions, safety
- **Nature**: Parks, wildlife, scenic routes, photography
- **Shopping**: Markets, malls, local crafts, souvenirs
- **Education**: Learning experiences, workshops, courses

### Performance Optimization

#### Efficient Tool Usage
- Start with broader searches, then narrow down
- Combine related queries when possible
- Cache frequently used information
- Implement smart retry logic

#### Response Time Management
- Use appropriate temperature settings
- Limit tool calls for simple queries
- Implement timeouts for long-running operations
- Optimize search query structure

#### Memory Management
- Clear unnecessary variables
- Limit result set sizes
- Use generators for large datasets
- Monitor memory usage

### Error Handling

#### Graceful Degradation
```python
try:
    result = tool.invoke(parameters)
    if not result:
        return "No information available at this time"
    return result
except Exception as e:
    return f"Service temporarily unavailable: {str(e)}"
```

#### User Communication
- Provide clear error messages
- Suggest alternatives when services fail
- Maintain user experience continuity
- Log errors for debugging

#### Fallback Strategies
```python
def search_with_fallback(primary_query, fallback_query):
    try:
        result = search.invoke(primary_query)
        if result:
            return result
    except:
        pass
    
    try:
        return search.invoke(fallback_query)
    except:
        return "Information not available"
```

---

## Advanced Features

### Custom Tool Development

#### Creating New Tools
```python
@tool
def your_custom_tool(parameter: str) -> str:
    """
    Tool description for the AI agent.
    
    Args:
        parameter (str): Parameter description
    
    Returns:
        str: Return value description
    """
    try:
        # Implementation logic
        result = process_parameter(parameter)
        return f"Processed result: {result}"
    except Exception as e:
        return f"Error processing {parameter}: {str(e)}"
```

#### Integration Guidelines
- Add to tools list
- Update system prompt if needed
- Test thoroughly before deployment
- Document tool capabilities
- Handle edge cases

### Multi-language Support

#### International Destinations
- Handle non-English city names
- Support local currency symbols
- Adapt to regional preferences
- Consider cultural differences

```python
# Example: Multi-language city name handling
CITY_TRANSLATIONS = {
    "München": "Munich",
    "Köln": "Cologne",
    "中国": "China",
    "日本": "Japan"
}

def normalize_city_name(city_name):
    return CITY_TRANSLATIONS.get(city_name, city_name)
```

### Seasonal Adjustments

#### Weather-based Planning
- Consider seasonal attractions
- Adjust clothing recommendations
- Account for seasonal pricing
- Include seasonal events

```python
def get_seasonal_recommendations(destination, month):
    seasonal_data = {
        "summer": ["beaches", "outdoor activities", "festivals"],
        "winter": ["indoor attractions", "winter sports", "museums"],
        "spring": ["gardens", "hiking", "cultural events"],
        "autumn": ["foliage tours", "harvest festivals", "comfortable walking"]
    }
    
    season = get_season_by_month(month)
    return seasonal_data.get(season, [])
```

### Caching and Optimization

#### Result Caching
```python
import functools
from datetime import datetime, timedelta

@functools.lru_cache(maxsize=100)
def cached_search(query, cache_duration_hours=6):
    # Implement cache with expiration
    return search.invoke(query)
```

#### Batch Processing
```python
def batch_hotel_search(destinations, budget_range):
    results = {}
    for dest in destinations:
        results[dest] = search_hotels(dest, budget_range)
    return results
```

### Real-time Updates

#### Live Data Integration
- Weather updates
- Currency fluctuations
- Event schedules
- Transportation delays

#### Notification System
```python
def check_for_updates(trip_data):
    updates = []
    
    # Check weather changes
    current_weather = get_current_weather(trip_data['destination'])
    if weather_changed(current_weather, trip_data['planned_weather']):
        updates.append("Weather forecast updated")
    
    # Check currency changes
    current_rate = get_exchange_rate(trip_data['from_currency'], trip_data['to_currency'])
    if rate_changed(current_rate, trip_data['planned_rate'], threshold=0.05):
        updates.append("Exchange rate changed significantly")
    
    return updates
```

---

## Conclusion

The AI Travel Agent & Expense Planner provides a comprehensive solution for intelligent travel planning. With its modular tool architecture, real-time data integration, and AI-powered orchestration, it delivers personalized travel experiences for any global destination and budget level.

### Key Benefits
- **Comprehensive Planning**: All aspects of travel covered in one system
- **Real-time Information**: Always up-to-date data for informed decisions
- **Cost Transparency**: Detailed expense breakdowns and budget planning
- **Personalization**: Tailored recommendations based on interests and budget
- **Scalability**: Easy to extend with new tools and features
- **Reliability**: Robust error handling and fallback mechanisms

### Future Enhancements
- Integration with booking platforms
- Machine learning for personalized recommendations
- Voice interface support
- Mobile app development
- Social features for trip sharing
- Integration with travel documents and visas
- Carbon footprint calculations
- Real-time collaboration for group trips

### Support and Maintenance
- Regular tool updates for accuracy
- Performance monitoring and optimization
- User feedback integration
- Security updates and compliance
- Documentation maintenance

For additional support, feature requests, or bug reports, refer to the troubleshooting section or extend the system with custom tools as needed.

---

*This documentation is maintained as part of the AI Travel Agent & Expense Planner project. Last updated: 2025*