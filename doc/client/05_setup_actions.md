## Set up actions

Define the actions that would be use in our app: loginRequest, register, getReceivedMessages, sendMessage, getCurrentUser, logout, updateMessage,

- In `src/redux/actions/auth.actions.js`, add:

```jsx
import * as types from "../constants/auth.constants";
import api from "../../apiService";
import { toast } from "react-toastify";

/**Login with email password*/
const loginRequest = ({ email, password }) => async (dispatch) => {
  dispatch({ type: types.LOGIN_REQUEST, payload: null });
  try {
    const res = await api.post("/auth/login", { email, password });
    dispatch({ type: types.LOGIN_SUCCESS, payload: res.data.data });
  } catch (error) {
    console.log(error);
    dispatch({ type: types.LOGIN_FAILURE, payload: error });
  }
};

/**Register new account*/
const register = ({ name, email, password }) => async (dispatch) => {
  dispatch({ type: types.REGISTER_REQUEST, payload: null });
  try {
    const res = await api.post("/users", { name, email, password });
    dispatch({ type: types.REGISTER_SUCCESS, payload: res.data.data });
    toast.success(`Thank you for your registration, ${name}!`);
  } catch (error) {
    dispatch({ type: types.REGISTER_FAILURE, payload: error });
  }
};

/**Get all messages of current user*/
const getReceivedMessages = (userId) => async (dispatch) => {
  dispatch({ type: types.GET_RECEIVED_MSG_REQUEST, payload: null });
  try {
    const res = await api.get(`/users/${userId}/messages`);
    dispatch({ type: types.GET_RECEIVED_MSG_SUCCESS, payload: res.data.data });
  } catch (error) {
    dispatch({ type: types.GET_RECEIVED_MSG_FAILURE, payload: error });
  }
};

/**Send message to other user*/
const sendMessage = ({ from, to, title, body }) => async (dispatch) => {
  dispatch({ type: types.SEND_MSG_REQUEST, payload: null });
  try {
    const res = await api.post(`/messages`, { from, to, title, body });
    dispatch({ type: types.SEND_MSG_SUCCESS, payload: res.data.data });
    dispatch(getReceivedMessages(from));
  } catch (error) {
    dispatch({ type: types.SEND_MSG_FAILURE, payload: error });
  }
};

/**update a message*/
const updateMessage = ({ message, currentUserId }) => async (dispatch) => {
  dispatch({ type: types.UPDATE_MSG_REQUEST, payload: null });
  try {
    const res = await api.put(`/messages`, message);
    dispatch({ type: types.UPDATE_MSG_SUCCESS, payload: res.data.data });
    dispatch(getReceivedMessages(currentUserId));
  } catch (error) {
    dispatch({ type: types.UPDATE_MSG_FAILURE, payload: error });
  }
};

/**Get current user infomation*/

const getCurrentUser = (accessToken) => async (dispatch) => {
  dispatch({ type: types.GET_CURRENT_USER_REQUEST, payload: null });
  try {
    api.defaults.headers.common["authorization"] = accessToken;
    const res = await api.get(`/users/me`);
    dispatch({ type: types.GET_CURRENT_USER_SUCCESS, payload: res.data.data });
  } catch (error) {
    dispatch({ type: types.GET_CURRENT_USER_FAILURE, payload: error });
  }
};

/**log out*/
const logout = () => (dispatch) => {
  localStorage.removeItem("accessToken");
  delete api.defaults.headers.common["authorization"];
  dispatch({ type: types.LOGOUT_REQUEST, payload: null });
};

const authActions = {
  loginRequest,
  register,
  getReceivedMessages,
  sendMessage,
  getCurrentUser,
  logout,
  updateMessage,
};
export default authActions;
```

Good job! [Back to instructions](/README.md)
