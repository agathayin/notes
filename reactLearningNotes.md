Learning notes by reading others' codes

## [codes](https://github.com/poulami-saha/frontend-mentor-product-list-with-cart/)  
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
the first example shares similar logic with redux and is easy to add new actions.
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
another example using context [code](https://github.com/Rgit915/product-list-with-cart) 
this one is similar to regular parent component and easy to read.
```
"use client";

import { createContext, useState, useContext } from "react";

// Create the Cart Context
const CartContext = createContext();

// Create a provider component
export function CartProvider({ children }) {
  const [cartItems, setCartItems] = useState([]);
  const addToCart = (item) => {
    // preItems is prevState, item is newState
    setCartItems((prevItems) => {
      const existingItem = prevItems.find(
        (cartItem) => cartItem.name === item.name
      );
      if (existingItem) {
        return prevItems.map((cartItem) =>
          cartItem.name === item.name
            ? { ...cartItem, quantity: cartItem.quantity + 1 }
            : cartItem
        );
      }
      return [...prevItems, { ...item, quantity: 1 }];
    });
  };
  // other functions

  return (
    <CartContext.Provider value={{ cartItems, addToCart, removeFromCart, clearCart, decrementQuantity }}>
      {children}
    </CartContext.Provider>
  );
}

// Custom hook to use the CartContext
export const useCart = () => {
  const context = useContext(CartContext);
  if (!context) {
    throw new Error("useCart must be used within a CartProvider");
  }
  return context;
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

## [codes]([https://github.com/poulami-saha/frontend-mentor-product-list-with-cart/](https://github.com/AD9-1/product-list))  

[redux doc](https://redux.js.org/tutorials/quick-start)
#### redux function
```
export const incrementCart = (item) => ({
  type: "increment",
  payload: item,
});
```
usage  
```
// imports
import { useDispatch, useSelector } from "react-redux";
import { addToCart, incrementCart, decrementCart } from "../../cartReducer";
// fn
const handleIncrement = () => {
    dispatch(incrementCart(singleItem));
    setClicked(true);
  };
```
#### redux store
```
import { legacy_createStore as createStore } from "redux";

const cartReducer = (state = initialState, action) => {
  switch (action.type) {
    case "addtocart":
      return {
        isModalOpen:false,
        cart: [
          ...state.cart,
          {
            ...action.payload,
            quantity: 1,
            totalprice: parseFloat(action.payload.price),
          },
        ],
      };
    case "increment":
      return {
        ...state,
        cart: state.cart.map((item) => {
          if (action.payload.name === item.name) {
            const newQuantity = item.quantity + 1;
            return {
              ...item,
              quantity: newQuantity,
              totalprice: newQuantity * parseFloat(item.price),
            };
          }
          return item;
        }),
      };
  // other cases
    default:
      return state;
  }
};
const store = createStore(cartReducer);
export default store;
```
