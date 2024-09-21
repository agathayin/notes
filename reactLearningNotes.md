Learning notes by reading others' codes


[codes](https://github.com/poulami-saha/frontend-mentor-product-list-with-cart/)  
### assets: fonts and images  
### components:  
#### arrow function
```
import styles from "./AddToCartButton.module.css";
// otherImport

interface AddToCartProps {
  isItemInCart: boolean;
  count: number;
  addToCart: () => void;
  removeFromCart: () => void;
}
// type for props are in <>
const AddToCart: React.FC<AddToCartProps> = ({
  isItemInCart,
  count,
  addToCart,
  removeFromCart,
}) => {
  // return ui
  );
};
// return type after ():
export const useCart = (): CartContextType => {
  const context = useContext(CartContext);
  if (!context) {
    throw new Error("useCart must be within a provider");
  }
  return context;
};

```
### context  
to use context, contextProvider and useContext are both required.  
See MERN for another example of user info and signin
#### reducer
reducer is later used in context provider
```
type CartAction =
  | { type: "ADD_TO_CART"; payload: CartItem }
  | { type: "REMOVE_FROM_CART"; payload: CartItem }
  | { type: "DELETE_ITEM"; payload: CartItem }
  | { type: "CLEAR_CART" };
const cartReducer = (state: CartState, action: CartAction): CartState => {
  switch (action.type) {
    case "ADD_TO_CART": {
      // return value
    }
    // ...other cases
    default:
      // return value;
  }
};
```
#### context provider
all wrapped components are able to use context
```
interface CartProviderProps {
  children: ReactNode;
}
export const CartProvider: React.FC<CartProviderProps> = ({ children }) => {
  const [state, dispatch] = useReducer(cartReducer, initialCartState);
  return (
    <CartContext.Provider value={{ state, dispatch }}>
      {children}
    </CartContext.Provider>
  );
};
```
usage  
in `index.tsx` 
```
root.render(
  <React.StrictMode>
    <CartProvider>
      <App />
    </CartProvider>
  </React.StrictMode>
);
```
#### use context
```
interface CartContextType {
  state: CartState;
  dispatch: Dispatch<CartAction>;
}
const CartContext = createContext<CartContextType | undefined>(undefined);
export const useCart = (): CartContextType => {
  const context = useContext(CartContext);
  if (!context) {
    throw new Error("useCart must be within a provider");
  }
  return context;
};
```
usage (in components)  
```
import { CartItem, useCart } from "../context/CartContext";
// this is set in context provider
const { state, dispatch } = useCart();
const handleClose = () => {
    setOpen(false);
    dispatch({ type: "CLEAR_CART" });
};
```


### hooks(useScreenWidth): 
```
import { useState, useEffect } from "react";

const useScreenWidth = () => {
  const [screenWidth, setScreenWidth] = useState(window.innerWidth);

  useEffect(() => {
    const handleResize = () => {
      setScreenWidth(window.innerWidth);
    };

    window.addEventListener("resize", handleResize);

    // Cleanup listener on unmount
    return () => {
      window.removeEventListener("resize", handleResize);
    };
  }, []);

  return screenWidth;
};

export default useScreenWidth;
```
usage  
```
const screenWidth = useScreenWidth();
useEffect(() => {
  setImageSrc(() => {
    if (screenWidth < 768) {
      return desert.image.mobile;
    }
    if (screenWidth < 960 && screenWidth >= 768) {
      return desert.image.tablet;
    }
    return desert.image.desktop;
  });
}, [desert, screenWidth]);
```
### model: 
1. mannually import all images from the folder "assets". `import baklavaThumbnail from "../assets/images/image-baklava-thumbnail.jpg";`
2. interfaces
