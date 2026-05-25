st = startard texture coordinate
```glsl
vec2 st = gl_FragCoord.xy/u_resolution.xy;
```
- Normalizes the pixel coordinates (`gl_FragCoord.xy`) from a range of `(0, resolution)` to `(0.0, 1.0)`, making coordinate handling resolution-independent.
    
- `st` (standard texture coordinate notation) now represents the pixel’s position in normalized space.