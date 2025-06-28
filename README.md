# 🏠 Zillow Real Estate API

A powerful, production-ready FastAPI application that scrapes Zillow real estate data to find subject properties and comparable sales (comps). Perfect for real estate professionals, investors, and developers who need property data at scale.

## 🌟 Features

### Core Functionality
- 🎯 **Subject Property Search** - Find properties matching specific criteria
- 📊 **Comparable Sales Analysis** - Get up to 10 recent sold properties for comparison
- 🗺️ **Geographic Boundary Control** - Restrict searches to specific map bounds
- 💰 **Price Range Filtering** - Set minimum and maximum price limits
- 🏘️ **City/State Based Search** - Search by location with intelligent formatting

### Technical Features
- ⚡ **FastAPI Framework** - High-performance async API with automatic docs
- 🔄 **Dual Request Methods** - Support for both GET and POST requests
- 🌐 **CORS Enabled** - Cross-origin resource sharing for web applications
- 🛡️ **Robust Error Handling** - Graceful fallbacks and mock data generation
- 📝 **Interactive Documentation** - Auto-generated Swagger UI
- 🚀 **Railway Ready** - One-click deployment configuration

## 🚀 Quick Start

### Prerequisites
- Python 3.8+
- pip package manager

### Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd zillow-real-estate-api
   ```

2. **Install dependencies**
   ```bash
   pip install fastapi uvicorn requests beautifulsoup4 pydantic
   ```

3. **Run the application**
   ```bash
   python main.py
   ```

4. **Access the API**
   - API Base URL: `http://localhost:8000`
   - Interactive Docs: `http://localhost:8000/docs`
   - Alternative Docs: `http://localhost:8000/redoc`

## 📖 API Documentation

### Base Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET    | `/`      | API information and version |
| GET    | `/health` | Health check endpoint |
| GET    | `/search` | Search properties (query parameters) |
| POST   | `/search` | Search properties (JSON body) |

### Search Properties

#### GET Request
```http
GET /search?city={city}&state={state}&min_price={min}&max_price={max}&west={west}&east={east}&south={south}&north={north}
```

**Parameters:**
- `city` (required): City name (e.g., "Los Angeles")
- `state` (required): State abbreviation (e.g., "CA", "NY")
- `min_price` (required): Minimum price in dollars
- `max_price` (required): Maximum price in dollars
- `west` (optional): Western longitude boundary
- `east` (optional): Eastern longitude boundary
- `south` (optional): Southern latitude boundary
- `north` (optional): Northern latitude boundary

#### POST Request
```http
POST /search
Content-Type: application/json

{
  "city": "Los Angeles",
  "state": "CA",
  "min_price": 500000,
  "max_price": 1000000,
  "map_bounds": {
    "west": -118.5,
    "east": -118.2,
    "south": 34.0,
    "north": 34.2
  }
}
```

## 💡 Usage Examples

### Basic Property Search
```bash
# Search for properties in Austin, TX between $300K-$600K
curl "http://localhost:8000/search?city=Austin&state=TX&min_price=300000&max_price=600000"
```

### Geographic Boundary Search
```bash
# Search with specific map bounds in Miami, FL
curl "http://localhost:8000/search?city=Miami&state=FL&min_price=400000&max_price=800000&west=-80.3&east=-80.1&south=25.7&north=25.8"
```

### POST Request Example
```bash
curl -X POST "http://localhost:8000/search" \
  -H "Content-Type: application/json" \
  -d '{
    "city": "Seattle",
    "state": "WA",
    "min_price": 600000,
    "max_price": 1200000,
    "map_bounds": {
      "west": -122.5,
      "east": -122.2,
      "south": 47.5,
      "north": 47.7
    }
  }'
```

## 📊 Response Format

### Successful Response
```json
{
  "subject_property": "3 bed 2 bath 1,450 square feet. Listed for $575,000. https://www.zillow.com/homedetails/...",
  "comparables": [
    "3 bed 2 bath 1,380 square feet. Sold for $560,000. https://www.zillow.com/homedetails/...",
    "4 bed 2.5 bath 1,520 square feet. Sold for $585,000. https://www.zillow.com/homedetails/..."
  ],
  "total_comps_found": 10,
  "error": null
}
```

### Error Response
```json
{
  "subject_property": null,
  "comparables": [],
  "total_comps_found": 0,
  "error": "No subject property found matching criteria"
}
```

## 🗺️ Map Bounds Usage

Map bounds allow you to restrict property searches to specific geographic areas using latitude and longitude coordinates.

### Finding Coordinates
1. **Google Maps**: Right-click → "What's here?" → Copy coordinates
2. **Zillow Map**: Use browser developer tools to inspect map bounds
3. **Bounding Box Tool**: Use tools like [bboxfinder.com](http://bboxfinder.com)

### Coordinate Format
- **West/East**: Longitude values (-180 to 180)
- **South/North**: Latitude values (-90 to 90)
- **Example**: Los Angeles downtown area
  - West: -118.3, East: -118.2
  - South: 34.0, North: 34.1

## 🔧 Configuration

### Environment Variables
```bash
PORT=8000  # Server port (default: 8000)
```

### Customization Options
- **Request Headers**: Modify user agent and headers in `ZillowRealEstateAPI.__init__()`
- **Search Filters**: Adjust filterState parameters in search methods
- **Mock Data**: Customize fallback data generation in `_generate_mock_properties()`
- **Rate Limiting**: Add delays between requests if needed

## 🚀 Deployment

### Railway Deployment
1. Connect your GitHub repository to Railway
2. Railway will automatically detect the Python application
3. Set environment variables if needed
4. Deploy with one click

### Manual Deployment
```bash
# Using Gunicorn
pip install gunicorn
gunicorn main:app --host 0.0.0.0 --port $PORT

# Using Uvicorn (production)
uvicorn main:app --host 0.0.0.0 --port $PORT --workers 4
```

### Docker Deployment
```dockerfile
FROM python:3.9-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .
EXPOSE 8000

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

## 🛠️ Development

### Project Structure
```
├── main.py              # Main FastAPI application
├── requirements.txt     # Python dependencies
├── README.md           # Project documentation
└── .gitignore          # Git ignore rules
```

### Key Classes
- **`Property`**: Data structure for property information
- **`MapBounds`**: Geographic boundary coordinates
- **`ZillowRealEstateAPI`**: Core scraping and data processing logic

### Adding Features
1. **New Filters**: Extend `filterState` in search query
2. **Additional Data**: Modify `Property` dataclass and parsing methods
3. **New Endpoints**: Add routes in the FastAPI section

## 🔍 Troubleshooting

### Common Issues

**No Properties Found**
- Check if city/state spelling is correct
- Verify price range is realistic for the area
- Try without map bounds first

**Connection Errors**
- Zillow may be blocking requests - check user agent
- Network issues - verify internet connection
- Rate limiting - add delays between requests

**Mock Data Returned**
- This is normal fallback behavior
- Indicates real Zillow data couldn't be fetched
- Mock data follows same format for testing

### Debug Mode
```python
# Add logging for debugging
import logging
logging.basicConfig(level=logging.DEBUG)
```

## 📋 Requirements

### Python Dependencies
```txt
fastapi>=0.68.0
uvicorn>=0.15.0
requests>=2.25.0
beautifulsoup4>=4.9.0
pydantic>=1.8.0
```

### System Requirements
- Python 3.8 or higher
- 512MB RAM minimum
- Network access for Zillow requests

## ⚖️ Legal Considerations

- **Web Scraping**: This tool scrapes public data from Zillow
- **Rate Limiting**: Implement delays to be respectful of Zillow's servers
- **Data Usage**: Ensure compliance with Zillow's Terms of Service
- **Commercial Use**: Review legal requirements for commercial applications

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Development Guidelines
- Follow PEP 8 style guidelines
- Add tests for new features
- Update documentation
- Ensure backward compatibility

## 📈 Performance Tips

- **Caching**: Implement Redis for frequently requested data
- **Async Processing**: Use background tasks for bulk operations
- **Database**: Store results in PostgreSQL for historical analysis
- **Load Balancing**: Use multiple instances for high traffic

## 🆘 Support

- **Documentation**: Check `/docs` endpoint for interactive API docs
- **Issues**: Report bugs via GitHub issues
- **Questions**: Use GitHub discussions for questions
- **Updates**: Watch repository for latest features

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- [FastAPI](https://fastapi.tiangolo.com/) for the excellent web framework
- [Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/) for HTML parsing
- [Railway](https://railway.app/) for easy deployment platform

---

**⭐ Star this repository if it helped you!**

*Built with ❤️ for the real estate community*
