# 🎮 Game Hub - Interactive Game Collection

**Game Hub** is a vibrant, all-in-one game platform featuring two exciting experiences! Choose between **Door Quest** for an interactive quiz adventure, or **Spin the Wheel** for random selection fun. Both games offer beautiful designs, smooth animations, and responsive layouts.

![Game Hub](https://img.shields.io/badge/Game-Game%20Hub-brightgreen) ![Vue.js](https://img.shields.io/badge/Vue.js-3.4.21-4FC08D) ![Vite](https://img.shields.io/badge/Vite-7.1.9-646CFF) ![Tailwind CSS](https://img.shields.io/badge/Tailwind%20CSS-3.4.18-38B2AC)

## 🎮 Features

### 🚪 **Door Quest Game**
- **Interactive Door Interface**: Each question is hidden behind a uniquely colored door
- **3D Door Animation**: Doors swing open with realistic perspective effects
- **Question & Answer System**: Click doors to reveal educational questions with TRUE/FALSE options
- **Prize System**: Win prizes for correct answers!
- **Responsive Design**: Optimized for all devices

### 🎡 **Spin the Wheel Game**
- **Random Selection**: Spin a colorful wheel to randomly select winners
- **Custom Name Lists**: Add up to 34 names to your wheel
- **Smart Tracking**: Automatically tracks selected names to prevent duplicates
- **Beautiful Animations**: Smooth spinning animations with vibrant colors
- **Visual Feedback**: Clear display of winner with celebration effects

### 📚 **Question Management System (Door Quest)**
- **Add New Questions**: Create custom questions with answers
- **Edit Questions**: Modify existing questions easily
- **Delete Questions**: Remove questions with confirmation
- **Smart Numbering**: Automatic sequential ID management
- **Statistics Dashboard**: Track total questions, characters, and more

### 🎨 **Beautiful Design**
- **Gradient Backgrounds**: Colorful gradients with floating decorations
- **Animated UI**: Smooth transitions and hover effects
- **Rainbow Borders**: Multi-colored borders on cards and modals
- **Fun Animations**: Bouncing, floating, and fade-in effects throughout
- **Student-Friendly UI**: Bright colors, playful typography, and engaging visuals

### 🏠 **Landing Page**
- **Game Selection**: Beautiful card-based selection screen
- **Welcome Message**: Friendly greeting and brief descriptions
- **Quick Navigation**: Easy access to both games from any page
- **Consistent Design**: Matches overall app aesthetic

## 🚀 Getting Started

### Prerequisites
- Node.js (version 14 or higher)
- npm or yarn package manager

### Installation

1. **Clone the repository**
   ```bash
   git clone <your-repo-url>
   cd websiteactivity
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Start the development server**
   ```bash
   npm run dev
   ```

4. **Open your browser**
   Navigate to `http://localhost:5173` to see the application

### Build for Production

```bash
npm run build
```

The built files will be in the `dist/` directory.

## 📱 Usage Guide

### 🏠 Getting Started

1. **Open Game Hub**: The app opens to a beautiful landing page
2. **Choose a Game**: Click on either "Door Quest" or "Spin the Wheel" card
3. **Start Playing**: Each game has its own interface and features
4. **Return to Home**: Click "Home" in the menu to return to the landing page

### 🚪 Playing Door Quest

1. **Select Door Quest**: Choose from the landing page or menu
2. **Welcome Screen**: Read the instructions (you can skip this)
3. **Choose a Door**: Click on any colored door to reveal a question
4. **Answer Questions**: Select TRUE or FALSE for each question
5. **Win Prizes**: Correct answers unlock prize rewards!
6. **Manage Questions**: Use the menu to add, edit, or delete questions

### 🎡 Playing Spin the Wheel

1. **Select Spin the Wheel**: Choose from the landing page or menu
2. **View Names**: See all 34 names on the colorful wheel
3. **Spin**: Click the center "SPIN" button
4. **Watch It Spin**: Enjoy the smooth animation
5. **See Winner**: The selected name is displayed with celebration
6. **Spin Again**: Keep spinning until all names are selected
7. **Reset**: Click "Reset & Start Over" to start fresh

### 📝 Managing Questions (Door Quest)

1. **Access Manager**: Click the hamburger menu (☰) and select "📚 Manage Questions"
2. **View Statistics**: See total questions, characters, and organization stats
3. **Add Questions**: 
   - Click "Add New" tab
   - Enter your question and answer
   - Click "Save Question"
4. **Edit Questions**: Click "Edit" on any question card
5. **Delete Questions**: Click "Delete" and confirm removal

## 🏗️ Project Structure

```
game-hub/
├── src/
│   ├── components/
│   │   ├── Door.vue           # Individual door component with question modal
│   │   ├── QuestionManager.vue # Question management interface
│   │   └── SpinWheel.vue      # Spin the wheel game component
│   ├── App.vue               # Main application with landing page
│   ├── main.js              # Application entry point
│   └── style.css            # Global styles and Tailwind imports
├── public/
│   └── vite.svg             # Vite logo
├── dist/                    # Built files (generated)
├── package.json             # Dependencies and scripts
├── tailwind.config.js       # Tailwind CSS configuration
├── postcss.config.cjs       # PostCSS configuration
├── vite.config.js           # Vite build configuration
└── README.md               # This file
```

## 🎨 Design System

### Color Palette
- **Primary Gradients**: Peach (#ffecd2) to Turquoise (#a8edea)
- **Door Colors**: 10 unique colors including brown, green, blue, red, orange, purple, teal, pink, lime, and orange-red
- **Accent Colors**: 
  - Gold (#FFD700) for highlights
  - Orange (#FF5722) for buttons
  - Green (#4CAF50) for success states
  - Blue (#2196F3) for information

### Typography
- **Primary Font**: Inter, Segoe UI, system fonts
- **Game Titles**: Bold, large text with colorful shadows
- **Questions**: Sans-serif, readable font sizes
- **Buttons**: Uppercase, bold text with letter spacing

### Animations
- **Door Opening**: 3D perspective rotation
- **Floating Elements**: Subtle up-and-down movement
- **Button Interactions**: Hover effects with scaling and color changes
- **Text Effects**: Wiggle, shake, and bounce animations

## 📱 Responsive Design

### Desktop (1024px+)
- 3-column door grid layout
- Full-size question modals
- Hover effects and animations

### Tablet (768px - 1024px)
- 2-column door grid layout
- Optimized modal sizing
- Touch-friendly interactions

### Mobile (640px and below)
- 2-column door grid layout
- Full-width question modals
- Optimized touch targets (44px minimum)
- Reduced padding and margins

### Ultra-Small Mobile (480px and below)
- Maximum space utilization
- Minimal padding
- Compressed text and elements

## 🛠️ Technical Details

### Framework & Libraries
- **Vue.js 3.4.21**: Progressive JavaScript framework
- **Vite 7.1.9**: Fast build tool and development server
- **Tailwind CSS 3.4.18**: Utility-first CSS framework
- **PostCSS**: CSS processing
- **Autoprefixer**: Automatic vendor prefixing

### Key Features
- **Composition API**: Modern Vue.js development approach
- **Reactive Data**: Real-time updates with Vue's reactivity system
- **Component Architecture**: Modular, reusable components
- **CSS Custom Properties**: Dynamic color generation for doors
- **Touch Optimization**: Mobile-first design with touch interactions

### Performance Optimizations
- **Lazy Loading**: Components load as needed
- **Efficient Animations**: CSS transforms and transitions
- **Responsive Images**: Optimized for different screen sizes
- **Minimal Bundle**: Tree-shaking and code splitting

## 🎯 Educational Use Cases

### Health Education
- Physical health practices
- Mental health awareness
- Holistic wellness concepts
- Stress management techniques

### Customizable Content
- Add questions for any subject in Door Quest
- True/False or multiple choice formats
- Custom name lists for Spin the Wheel
- Visual and interactive learning
- Progress tracking capabilities

## 🔧 Customization

### Adding New Questions
1. Navigate to Question Manager
2. Click "Add New" tab
3. Enter question text and answer
4. Save to add to your collection

### Modifying Colors
Edit the `doorColors` array in `App.vue`:
```javascript
const doorColors = [
  '#8B4513', // Saddle Brown
  '#228B22', // Forest Green
  // Add your custom colors here
]
```

### Styling Changes
- Modify `tailwind.config.js` for theme customization
- Edit component styles in individual `.vue` files
- Update global styles in `src/style.css`

## 🚀 Deployment

### Static Hosting (Recommended)
1. Run `npm run build`
2. Upload `dist/` folder to your hosting service
3. Configure your server to serve `index.html` for all routes

### Popular Hosting Options
- **Netlify**: Drag and drop the `dist/` folder
- **Vercel**: Connect your GitHub repository
- **GitHub Pages**: Use GitHub Actions for automatic deployment
- **Firebase Hosting**: Use Firebase CLI for deployment

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- **Vue.js Team**: For the amazing framework
- **Tailwind CSS**: For the utility-first CSS framework
- **Vite Team**: For the lightning-fast build tool
- **Educational Community**: For inspiring student-friendly design

## 📞 Support

If you encounter any issues or have questions:

1. Check the [Issues](https://github.com/your-repo/issues) page
2. Create a new issue with detailed information
3. Include browser version, device type, and steps to reproduce

---

**Made with ❤️ for education and learning**

*Transform your classroom into an interactive adventure with Door Quest!*