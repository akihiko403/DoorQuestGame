# 🚪 Door Quest - Interactive Educational Game

**Door Quest** is a vibrant, student-friendly educational quiz application that transforms learning into an exciting adventure! Players click on colorful doors to reveal questions and test their knowledge in a fun, interactive environment.

![Door Quest Game](https://img.shields.io/badge/Game-Door%20Quest-brightgreen) ![Vue.js](https://img.shields.io/badge/Vue.js-3.4.21-4FC08D) ![Vite](https://img.shields.io/badge/Vite-7.1.9-646CFF) ![Tailwind CSS](https://img.shields.io/badge/Tailwind%20CSS-3.4.18-38B2AC)

## 🎮 Features

### 🎯 **Interactive Game Mode**
- **Colorful Door Interface**: Each question is hidden behind a uniquely colored door
- **3D Door Animation**: Doors swing open with realistic perspective effects
- **Question Reveal**: Click doors to reveal educational questions
- **Answer Discovery**: Interactive "Reveal Answer" button with fun animations
- **Responsive Design**: Optimized for desktop, tablet, and mobile devices

### 📚 **Question Management System**
- **Add New Questions**: Create custom questions with answers
- **Edit Questions**: Modify existing questions easily
- **Delete Questions**: Remove questions with confirmation
- **Smart Numbering**: Automatic sequential ID management
- **Statistics Dashboard**: Track total questions, characters, and more

### 🎨 **Beautiful Design**
- **Gradient Backgrounds**: Peach-to-turquoise gradient with floating door decorations
- **Rainbow Borders**: Multi-colored borders on question modals
- **Fun Animations**: Bouncing, floating, and fade-in effects throughout
- **Student-Friendly UI**: Bright colors, playful typography, and engaging visuals

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

### 🎮 Playing the Game

1. **Launch the Game**: Click the "▶ Play" button or use the hamburger menu
2. **Choose a Door**: Click on any colored door to reveal a question
3. **Read the Question**: Take your time to think about the answer
4. **Reveal Answer**: Click "Reveal Answer" to see if you're correct
5. **Close Door**: Click the red X button to return to the door selection

### 📝 Managing Questions

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
websiteactivity/
├── src/
│   ├── components/
│   │   ├── Door.vue           # Individual door component with question modal
│   │   └── QuestionManager.vue # Question management interface
│   ├── App.vue               # Main application component
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
- Add questions for any subject
- True/False or multiple choice formats
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