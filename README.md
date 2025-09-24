# Embodee Configurator Integration

A Vue.js application that integrates the Embodee Configurator API for interactive 3D apparel customization.

## Features

- **3D Product Viewer**: Interactive 3D model with real-time customization
- **Customization Panel**: Color swatches, graphic styles, and text options
- **Product Information**: Detailed product specs and customization summary
- **Responsive Design**: Optimized for desktop and mobile devices
- **Modern UI**: Clean, modern interface with Inter font family

## Project Structure

```
embodee-configurator/
├── src/
│   ├── components/
│   │   ├── EmbodeeConfigurator.vue    # Main configurator component
│   │   ├── CustomizationPanel.vue    # Left customization panel
│   │   └── ProductInfoPanel.vue      # Right product information panel
│   ├── App.vue                        # Main application component
│   ├── main.js                        # Application entry point
│   └── style.css                      # Global styles
├── index.html                         # HTML template
├── package.json                       # Dependencies and scripts
├── vite.config.js                     # Vite configuration
└── README.md                          # This file
```

## Installation

1. **Install dependencies:**
   ```bash
   npm install
   ```

2. **Start development server:**
   ```bash
   npm run dev
   ```

3. **Build for production:**
   ```bash
   npm run build
   ```

## Configuration

The application is configured to use the sandbox instance:
- **Workspace ID**: `qcxwobokwv`
- **Product Code**: `Tee00198468`

To change these values, update the `workspaceId` and `productCode` in `src/App.vue`.

## API Integration

The application integrates with the Embodee Configurator API using:

1. **Loader Script**: Loads from `https://beta.embodee.com/__/configurator/loader.js`
2. **Initialization**: Uses `EmbodeeLoader.init()` with configuration options
3. **Event Handling**: Subscribes to configurator events for real-time updates
4. **Component API**: Uses `setComponentValue()` for customization

## Components

### EmbodeeConfigurator.vue
- Loads the Embodee loader script
- Initializes the 3D configurator
- Handles all configurator events
- Provides methods for customization

### CustomizationPanel.vue
- Displays available customization options
- Color swatches for body colors
- Graphic style options
- Text customization controls
- Real-time updates based on UI structure

### ProductInfoPanel.vue
- Product title and description
- Product details and specifications
- Customization summary
- Action buttons (Add to Cart, Save Design, Share)
- Additional features display

## Styling

The application uses:
- **Font**: Inter (Google Fonts)
- **Colors**: Modern color palette with blue accents
- **Layout**: CSS Grid for responsive three-column layout
- **Components**: Card-based design with subtle shadows
- **Responsive**: Mobile-first approach with breakpoints

## Events

The application handles these Embodee Configurator events:
- `loadStart` - Loading begins
- `loadProgress` - Loading progress with percentage
- `loadEnd` - Loading complete
- `productReady` - All data loaded and ready
- `meshSelected` - User clicks on 3D model
- `componentValueChanged` - Component values updated
- `error` - Error handling

## Customization

### Adding New Customization Options
1. Update the `CustomizationPanel.vue` component
2. Add new sections for different customization types
3. Handle the corresponding events in the parent component

### Styling Changes
1. Modify `src/style.css` for global styles
2. Update component-specific styles in each Vue component
3. Adjust responsive breakpoints as needed

### API Configuration
1. Update configuration options in `EmbodeeConfigurator.vue`
2. Modify workspace and product settings in `App.vue`
3. Add new event handlers as needed

## Browser Support

- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+

## Development

The application uses:
- **Vue 3** with Composition API
- **Vite** for build tooling
- **Modern CSS** with CSS Grid and Flexbox
- **ES6+** JavaScript features

## Production Deployment

1. Build the application:
   ```bash
   npm run build
   ```

2. Deploy the `dist` folder to your web server

3. Ensure the Embodee loader script is accessible from your domain

## Troubleshooting

### Common Issues

1. **Configurator not loading**: Check network connectivity and CORS settings
2. **Customization not working**: Verify the UI structure is properly parsed
3. **Mobile layout issues**: Check responsive breakpoints and viewport settings

### Debug Mode

Enable debug logging by opening browser developer tools and checking the console for detailed event information.

## License

This project is licensed under the MIT License.
