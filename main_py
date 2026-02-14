from fastapi import FastAPI, File, UploadFile, HTTPException
from fastapi.responses import Response
from rembg import remove
from PIL import Image, ImageDraw
import io

app = FastAPI()

@app.get("/")
def root():
    return {"status": "ok"}

@app.post("/remove-background")
async def remove_background(file: UploadFile = File(...)):
    if not file.content_type.startswith("image/"):
        raise HTTPException(status_code=400, detail="Nur Bilddateien erlaubt")

    try:
        contents = await file.read()

        # Hintergrund entfernen
        result_bytes = remove(contents)

        # Transparentes PNG auf weißen Hintergrund legen
        fg = Image.open(io.BytesIO(result_bytes)).convert("RGBA")
        bg = Image.new("RGBA", fg.size, (255, 255, 255, 255))
        bg.paste(fg, mask=fg.split()[3])
        final = bg.convert("RGB")

        # Als PNG zurückgeben
        output = io.BytesIO()
        final.save(output, format="PNG")
        output.seek(0)

        return Response(content=output.read(), media_type="image/png")

    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))
