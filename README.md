# Auto-Holography-for-Videos-and-photos
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
import { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import axios from "axios";
import * as handTrack from "handtrackjs";
import { io } from "socket.io-client";

const socket = io("https://smilevaultx-api.com");

const AutoHolographicApp = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [holograms, setHolograms] = useState([]);
  const [scene, setScene] = useState(null);
  const [renderer, setRenderer] = useState(null);
  const [camera, setCamera] = useState(null);
  const [mesh, setMesh] = useState(null);
  const [model, setModel] = useState(null);
  const [video, setVideo] = useState(null);
  const [selectedColor, setSelectedColor] = useState("#ffffff");
  const [size, setSize] = useState(1);
  const [annotations, setAnnotations] = useState([]);
  const [textAnnotations, setTextAnnotations] = useState([]);
  const [currentText, setCurrentText] = useState("");
  const [savedHolograms, setSavedHolograms] = useState([]);
  const [inviteLink, setInviteLink] = useState("");
  const canvasRef = useRef();

  useEffect(() => {
    axios.get("https://smilevaultx-api.com/holograms/all").then((response) => {
      setSavedHolograms(response.data);
    });
  }, []);

  useEffect(() => {
    socket.emit("join-session", { room: "global_hologram_session" });

    socket.on("session-sync", (data) => {
      setAnnotations(data.annotations);
      setTextAnnotations(data.textAnnotations);
      setSelectedColor(data.selectedColor);
      setSize(data.size);
    });
  }, []);

  const generateInviteLink = () => {
    const link = `https://smilevaultx.com/session/${Date.now()}`;
    setInviteLink(link);
    alert(`Invite link generated: ${link}`);
  };

  const changeColor = (color) => {
    setSelectedColor(color);
    socket.emit("color-update", { color });
    syncSession();
  };

  const resizeHologram = (newSize) => {
    setSize(newSize);
    socket.emit("size-update", { size: newSize });
    syncSession();
  };

  const handleAnnotation = (event) => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(event.clientX, event.clientY, 5, 5);

    const annotation = { x: event.clientX, y: event.clientY };
    setAnnotations([...annotations, annotation]);
    socket.emit("annotation-update", annotation);
    syncSession();
  };

  const handleTextAnnotation = (event) => {
    const newText = { x: event.clientX, y: event.clientY, text: currentText };
    setTextAnnotations([...textAnnotations, newText]);
    socket.emit("text-annotation-update", newText);
    syncSession();
  };

  const syncSession = () => {
    const sessionData = {
      annotations,
      textAnnotations,
      selectedColor,
      size
    };
    socket.emit("sync-session", sessionData);
  };

  return (
    <div className="p-4 grid gap-4">
      <Card className="p-4">
        <CardContent className="grid gap-2">
          <motion.h1 className="text-xl font-bold" animate={{ scale: 1.1 }}>
            Auto Holographic Video & Photo App
          </motion.h1>
          <Button onClick={generateInviteLink} className="mt-2">Generate Invite Link</Button>
          {inviteLink && (
            <div className="mt-2 border p-2">
              Share this link: <a href={inviteLink} target="_blank" rel="noopener noreferrer">{inviteLink}</a>
            </div>
          )}
          <input
            type="text"
            value={currentText}
            onChange={(e) => setCurrentText(e.target.value)}
            placeholder="Type annotation text"
            className="border p-2 mt-2"
          />
          <canvas
            ref={canvasRef}
            onClick={handleTextAnnotation}
            width={window.innerWidth}
            height={window.innerHeight}
            className="border mt-4"
          />
        </CardContent>
      </Card>
    </div>
  );
};

export default AutoHolographicApp;
