# NyaayDrishti - Judicial Case Management System

## Project Overview

**NyaayDrishti** is a comprehensive judicial case management platform developed for HackXios 2025 by Team Trojans. The system streamlines court operations by providing role-based dashboards for lawyers, judges, and court masters, with intelligent case prioritization and automated daily cause list generation.

### Key Features

- **Multi-Role Authentication System**: Separate interfaces for Lawyers, Judges, and Court Masters
- **Intelligent Case Prioritization**: AI-powered priority scoring algorithm for optimal case scheduling
- **Automated Daily Cause List Generation**: Time-bin packing algorithm for efficient court time utilization
- **Real-time Case Management**: Track case status, hearings, and notifications
- **Document Management**: Handle vakalatnama, affidavits, and court documents
- **Defect Management**: Track and resolve case filing defects

## Technical Architecture

### Technology Stack

- **Backend**: Node.js with Express.js framework
- **Database**: MongoDB with Mongoose ODM
- **Authentication**: Passport.js with Local Strategy
- **Template Engine**: EJS with EJS-Mate
- **Session Management**: Express-Session with Connect-Flash
- **Security**: bcrypt for password hashing
- **Frontend**: Vanilla JavaScript with responsive CSS

### Project Structure

```
NyaayDrishti/
├── app.js                      # Main application entry point
├── package.json                # Dependencies and project metadata
├── controllers/                # Business logic controllers
│   ├── authController.js       # Authentication logic
│   ├── dashboardController.js  # Dashboard data and API endpoints
│   └── lawyer.js              # Lawyer-specific operations
├── model/                     # Database schemas
│   ├── lawyer.js              # Lawyer model with Passport integration
│   └── case.js                # Case model for legal cases
├── routes/                    # API route definitions
│   ├── authRoutes.js          # Authentication routes
│   ├── dashboardRoutes.js     # Dashboard routes
│   └── lawyer.js              # Lawyer-specific routes
├── middlewares/               # Custom middleware functions
│   ├── isAuth.js              # Authentication middleware
│   └── checkRole.js           # Role-based access control
├── utils/                     # Utility functions
│   ├── priorityEngine.js      # Case prioritization algorithm
│   ├── redirectbyRole.js      # Role-based redirection
│   └── wrapAsync.js           # Async error handling wrapper
├── views/                     # EJS templates
│   ├── auth/                  # Authentication pages
│   ├── lawyer/                # Lawyer dashboard views
│   ├── judge/                 # Judge dashboard views
│   ├── cMaster/               # Court Master views
│   └── includes/              # Shared template components
├── public/                    # Static assets
│   ├── CSS/                   # Stylesheets
│   ├── JS/                    # Client-side JavaScript
│   └── images/                # Static images
├── init/                      # Database initialization
│   ├── index.js               # Database setup
│   └── caseData.js            # Sample case data
└── data/                      # Temporary data storage
    └── tempusers.js           # Temporary user data
```

## Core Components

### 1. Authentication System

The system implements a robust authentication mechanism using Passport.js:

- **User Registration**: Lawyers register with Bar Council Registration Number
- **Login/Logout**: Secure session-based authentication
- **Role-Based Access**: Different access levels for Lawyers, Judges, Court Masters
- **Session Management**: Secure session handling with configurable timeouts

### 2. Database Models

#### Lawyer Model
```javascript
{
  email: String (unique, required),
  username: String (required),
  mobile: String,
  BarCouncilRegistrationNumber: Number (unique, required),
  specializations: [String],
  courts: [String],
  totalCases: Number (default: 0),
  activeCases: Number (default: 0),
  disposedCases: Number (default: 0),
  successRate: Number (default: 0),
  isActive: Boolean (default: true),
  vakalatnamaValidity: Date,
  profilePicture: String
}
```

#### Case Model
```javascript
{
  lawyerId: ObjectId (ref: 'Lawyer', required),
  caseType: String (required),
  courtType: String (required),
  caseNumber: String (required),
  petitioner: String (required),
  respondent: String (required),
  stage: String (required),
  nextHearingDate: Date (required),
  timeSlot: String (required),
  courtFee: Number,
  status: String (required),
  affidavitId: Number,
  vakalatnamaNumber: Number
}
```

### 3. Priority Engine Algorithm

The system implements an intelligent case prioritization algorithm using weighted scoring:

#### Priority Score Formula
```
Ps = (W_age × A) + (W_cat × C) + (W_stage × S) + (W_vul × V) + (W_adj × L)
```

**Weights:**
- W_age = 0.05 (Age factor)
- W_cat = 0.20 (Case category)
- W_stage = 0.40 (Case stage - most important)
- W_vul = 0.15 (Vulnerability factor)
- W_adj = 0.20 (Adjournment compensation)

**Factors:**
- **A (Age)**: Days since case filing, normalized to 0-1
- **C (Category)**: Case type weights (Habeas Corpus: 1.0, Bail: 0.95, Criminal: 0.80, etc.)
- **S (Stage)**: Case stage weights (Final Arguments: 0.95, Arguments: 0.75, Evidence: 0.50, etc.)
- **V (Vulnerability)**: Vulnerable party involvement (seniors, women, etc.)
- **L (Adjournment)**: Compensation for previously adjourned cases

### 4. Daily Cause List Generation

The system uses a time-bin packing algorithm to optimize court scheduling:

1. **Priority Calculation**: All pending cases are scored using the priority algorithm
2. **Time Estimation**: Each case gets an estimated hearing time based on type and stage
3. **Constraint Application**: 
   - Maximum 2 final argument cases per day
   - 15% buffer time for unexpected delays
   - Court time limits (default: 5 hours = 300 minutes)
4. **Bin Packing**: Cases are scheduled in priority order within time constraints

## API Endpoints

### Authentication Routes
- `GET /login` - Login page
- `POST /login` - Process login
- `GET /signup` - Registration page
- `POST /signup` - Process registration
- `POST /logout` - Logout user

### Dashboard Routes
- `GET /lawyerDashboard` - Lawyer dashboard page
- `GET /judgeDashboard` - Judge dashboard page
- `GET /cMasterDashboard` - Court Master dashboard page

### API Endpoints (Protected)
- `GET /api/dashboard-data` - Get lawyer dashboard statistics
- `GET /api/cases` - Get lawyer's cases
- `GET /api/notifications` - Get notifications
- `GET /api/defects` - Get case defects
- `GET /api/daily-cause-list` - Generate daily cause list
- `GET /api/case-priority/:caseId` - Get case priority details
- `POST /api/file-case` - File new case
- `POST /api/update-profile` - Update lawyer profile
- `POST /api/case-action/:caseId` - Court Master case actions

## Installation and Setup

### Prerequisites
- Node.js (v14 or higher)
- MongoDB (local or cloud instance)
- npm or yarn package manager

### Installation Steps

1. **Clone the repository**
```bash
git clone https://github.com/Bhargav-Bhamare/NyaayDrishti.git
cd NyaayDrishti
```

2. **Install dependencies**
```bash
npm install
```

3. **Set up MongoDB**
- Ensure MongoDB is running on `mongodb://localhost:27017/NyaayDrishti`
- Or update the connection string in `app.js`

4. **Initialize database (optional)**
```bash
node init/index.js
```

5. **Start the application**
```bash
node app.js
# or for development
nodemon app.js
```

6. **Access the application**
- Open browser and navigate to `http://localhost:8080`

## User Roles and Permissions

### Lawyer
- Register and manage profile
- File new cases
- View case status and hearings
- Receive notifications and defect alerts
- Access case history and statistics

### Judge
- View daily cause list
- Access case priority details
- Manage court proceedings
- Review case schedules

### Court Master
- Generate optimized daily cause lists
- Manage court time allocation
- Handle case scheduling conflicts
- Monitor court utilization

## Key Features in Detail

### 1. Case Filing System
- Automated case number generation
- Document upload support (vakalatnama, affidavits)
- Real-time status tracking
- Defect management and resolution

### 2. Notification System
- Real-time case updates
- Hearing reminders
- Defect alerts
- Status change notifications

### 3. Priority-Based Scheduling
- Intelligent case prioritization
- Time-optimized scheduling
- Constraint-based allocation
- Buffer time management

### 4. Dashboard Analytics
- Case statistics and metrics
- Success rate tracking
- Court utilization reports
- Performance analytics

## Security Features

- **Password Hashing**: bcrypt with salt rounds
- **Session Security**: HTTP-only cookies with expiration
- **Role-Based Access Control**: Middleware-based permission system
- **Input Validation**: Server-side validation for all inputs
- **CSRF Protection**: Flash messages and secure forms

## Development Guidelines

### Code Structure
- Follow MVC (Model-View-Controller) pattern
- Use async/await for database operations
- Implement proper error handling with try-catch blocks
- Use middleware for cross-cutting concerns

### Database Best Practices
- Use Mongoose schemas with validation
- Implement proper indexing for performance
- Use population for related data
- Handle connection errors gracefully

### Frontend Guidelines
- Responsive design for mobile compatibility
- Progressive enhancement approach
- Accessible UI components
- Clean separation of concerns

## Future Enhancements

### Planned Features
1. **Document Management System**: Full document upload and management
2. **Video Conferencing Integration**: Virtual court hearings
3. **Mobile Application**: Native mobile apps for iOS and Android
4. **Advanced Analytics**: Machine learning-based insights
5. **Multi-language Support**: Regional language support
6. **Integration APIs**: Third-party system integrations

### Technical Improvements
1. **Microservices Architecture**: Break down into smaller services
2. **Real-time Updates**: WebSocket implementation for live updates
3. **Caching Layer**: Redis for improved performance
4. **API Documentation**: Swagger/OpenAPI documentation
5. **Testing Suite**: Comprehensive unit and integration tests
6. **CI/CD Pipeline**: Automated deployment pipeline

## Troubleshooting

### Common Issues

1. **Database Connection Error**
   - Ensure MongoDB is running
   - Check connection string in `app.js`
   - Verify network connectivity

2. **Authentication Issues**
   - Clear browser cookies and session data
   - Check Passport.js configuration
   - Verify user credentials in database

3. **Port Already in Use**
   - Change port in `app.js` (default: 8080)
   - Kill existing processes using the port
   - Use environment variables for port configuration

4. **Missing Dependencies**
   - Run `npm install` to install all dependencies
   - Check for version compatibility issues
   - Clear `node_modules` and reinstall if needed

## Contributing

### Development Workflow
1. Fork the repository
2. Create a feature branch
3. Make changes with proper testing
4. Submit a pull request with detailed description
5. Ensure code follows project conventions

### Code Standards
- Use consistent indentation (2 spaces)
- Follow JavaScript ES6+ standards
- Add comments for complex logic
- Write meaningful commit messages
- Include proper error handling

## License

This project is licensed under the ISC License. See the repository for more details.

## Team

**Team Trojans** - HackXios 2025
- Project Repository: https://github.com/Bhargav-Bhamare/NyaayDrishti
- Issues: https://github.com/Bhargav-Bhamare/NyaayDrishti/issues

## Support

For support and questions:
1. Check the troubleshooting section above
2. Review the GitHub issues page
3. Contact the development team through the repository

---

*This documentation was generated for NyaayDrishti v1.0.0 - A comprehensive judicial case management system designed to modernize court operations and improve access to justice.*