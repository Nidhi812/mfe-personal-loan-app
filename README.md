# Module Federation Demo - Host & Remote Applications

This project demonstrates **Angular Module Federation** using two applications:
- **Host App** (port 4200): Container application
- **Personal Loan App** (port 4201): Remote micro-frontend

## 📋 Prerequisites

- Node.js (v18 or higher)
- npm (v9 or higher)
- Angular CLI (v20 or higher)

## 🏗️ Project Structure

```
Work_day_baja/
├── host-app/              # Host application (Container)
│   ├── src/
│   │   └── app/
│   │       ├── home/      # Landing page with Module Federation info
│   │       ├── dashboard/ # Optional dashboard
│   │       └── app.routes.ts  # Routing with lazy-loaded remote
│   └── webpack.config.js  # Module Federation config (consumes remote)
│
└── personal-loan-app/     # Remote application (Micro-frontend)
    ├── src/
    │   └── app/
    │       └── pages/
    │           └── personal-details/  # Exposed component
    └── webpack.config.js  # Module Federation config (exposes component)
```

## 🚀 How to Run

### Step 1: Install Dependencies

**For Host App:**
```bash
cd host-app
npm install
```

**For Personal Loan App (Remote):**
```bash
cd personal-loan-app
npm install
```

### Step 2: Start the Applications

**IMPORTANT:** Start the Remote app FIRST, then the Host app.

#### Terminal 1 - Start Remote (Personal Loan App)
```bash
cd personal-loan-app
npm start -- --port 4201
```
✅ Remote app will run on: `http://localhost:4201`

#### Terminal 2 - Start Host App
```bash
cd host-app
npm start -- --port 4200
```
✅ Host app will run on: `http://localhost:4200`

### Step 3: Access the Applications

- **Host App Home Page:** http://localhost:4200
- **Personal Loan App (Remote):** http://localhost:4201
- **Personal Loan via Host:** http://localhost:4200/loan

## 🎯 Features

### Host Application
- **Home Page:** Information about Module Federation architecture
- **Dynamic Loading:** Loads the Personal Loan App at runtime
- **Routing:** Angular Router with lazy-loaded remote modules
- **Responsive Design:** Mobile-first approach

### Personal Loan Application (Remote)
- **Form Features:**
  - Full name validation
  - Gender selection
  - Date of birth with age validation (18-80 years)
  - PAN card validation
  - Employment type selection
  - PIN code validation
  - Terms and conditions
- **Stepper Navigation:** 3-step form progress
- **Responsive Layout:** Desktop and mobile optimized

## 🔧 Module Federation Configuration

### Host App (webpack.config.js)
```javascript
module.exports = withModuleFederationPlugin({
  remotes: {
    // Consumes the personal-loan-app running on port 4201
    "personalLoanApp": "http://localhost:4201/remoteEntry.js",    
  },
  shared: {
    ...shareAll({ singleton: true, strictVersion: true, requiredVersion: 'auto' }),
  },
});
```

### Remote App (webpack.config.js)
```javascript
module.exports = withModuleFederationPlugin({
  name: 'personal-loan-app',
  filename: 'remoteEntry.js',
  exposes: {
    // Exposes the PersonalDetails component
    './PersonalDetailsModule': './src/app/pages/personal-details/personal-details.ts',
  },
  shared: {
    ...shareAll({ singleton: true, strictVersion: true, requiredVersion: 'auto' }),
  },
});
```

## 📱 Testing the Integration

1. Open `http://localhost:4200` in your browser
2. You'll see the **Home Page** with Module Federation information
3. Click the **"Open Personal Loan Application"** button
4. The remote app will load dynamically without page refresh
5. Fill out the form and test the validation

## 🐛 Troubleshooting

### Issue: Remote module not loading
**Solution:** 
- Ensure the remote app is running on port 4201
- Check the browser console for errors
- Verify the `remoteEntry.js` is accessible at `http://localhost:4201/remoteEntry.js`

### Issue: Port already in use
**Solution:**
```bash
# For host app
npm start -- --port 4300

# For remote app  
npm start -- --port 4301
```
Then update the `webpack.config.js` in host-app to point to the new port.

### Issue: Build errors
**Solution:**
```bash
# Clear node_modules and reinstall
rm -rf node_modules package-lock.json
npm install
```

## 📚 Key Concepts

### Module Federation Benefits
- ✅ **Independent Deployment:** Deploy host and remote separately
- ✅ **Team Autonomy:** Different teams can work on different apps
- ✅ **Shared Dependencies:** Avoid duplicate Angular bundles
- ✅ **Runtime Integration:** Load remotes dynamically at runtime
- ✅ **Version Management:** Control shared library versions

### How It Works
1. **Build Time:** Each app builds independently with its own bundle
2. **Runtime:** Host app loads the remote's `remoteEntry.js` file
3. **Module Loading:** Remote module is fetched and executed in the host
4. **Shared Libraries:** Angular core libraries are shared between apps
5. **Navigation:** Angular Router handles routing to remote components

## 🔗 Routing Configuration

### Host App Routes (app.routes.ts)
```typescript
{
  path: '',
  loadComponent: () => import('./home/home').then(m => m.Home),
},
{
  path: 'loan',
  loadComponent: () => loadRemoteModule({
    remoteEntry: 'http://localhost:4201/remoteEntry.js',
    exposedModule: './PersonalDetailsModule'
  }).then(m => m.PersonalDetails)
}
```

## 🎨 Styling Notes

- Host app uses gradient background with glassmorphism effects
- Remote app has its own independent styling
- Styles are scoped to each application
- No style conflicts between host and remote

## 📦 Build for Production

### Build Remote First
```bash
cd personal-loan-app
npm run build
```

### Build Host
```bash
cd host-app
npm run build
```

### Deploy
Deploy the `dist/` folders to your hosting service. Ensure the remote URL in `webpack.config.js` points to the production remote URL.

## 🤝 Contributing

This is a demo project for learning Module Federation. Feel free to:
- Add more remote applications
- Enhance the form with additional fields
- Implement backend API integration
- Add unit and integration tests

## 📄 License

MIT License - feel free to use this for learning and development.

## 🙋 Support

If you encounter issues:
1. Check the browser console for errors
2. Verify both apps are running on correct ports
3. Clear browser cache
4. Restart both development servers

---

**Built with ❤️ using Angular 20 & Module Federation**
