# Coaster Ranker

A solution for ranking your coaster credits that accounts for the implicit bias of trying to have the right opinion.  

Derived from [this repository](https://github.com/coasterrankerburner-gif/Claude-Pairwise-Coaster-Ranker) and made non-Claude centric, this tool lets you import your coaster list (via `.csv`, `.json`, `.md`, or plaintext) and sequentially rank them 1-on-1 to produce an unbiased ranking. Choose to run through your whole list of credits, your top 50, 25, or 10. It's best to include the park name along with the coaster, especially for ambiguously named rides (e.g., Batman, Goliath).  

Live site is here: https://coasters.cmyksoda.cc  

## Self-Hosting

Hosting this yourself is dead simple and lives in one docker container. A `docker-compose.yml` is already provided.

1. Clone this repository.
2. Run `docker compose up -d`
3. Coaster ranker now lives at http://localhost:8192
### Optional: pre-load coaster images

Out of the box the server fetches images from RCDB on demand, so the first person to import a list waits a moment per coaster. You can pre-load whole park chains instead:

```
docker exec -it coaster-ranker python3 /app/warm_cache.py --list
docker exec -it coaster-ranker python3 /app/warm_cache.py --all
```

`--all` covers Cedar Fair, Six Flags, SeaWorld/Busch Gardens, Herschend, Hersheypark, Universal, Disney, Merlin, Palace, Parques Reunidos and Compagnie des Alpes, plus a bunch more  well-known independents worldwide — 219 parks, around 1,200 coasters and ~210 MB — and takes about 40 minutes. Images land in `./cache`, mounted as a volume, so rebuilding the container never re-downloads anything.

**Please be kind to RCDB.** `MIN_INTERVAL` (default `0.34`) is the minimum gap in seconds between outbound requests, and every visitor's imports share it.

### Putting it in front of other people

If you're exposing this via a tunnel or reverse proxy, set `ALLOWED_ORIGINS` in `docker-compose.yml` to your domain. It defaults to `*`, which lets any website drive your API from its visitors' browsers.

Each visitor's coaster list and ranking live only in their own browser (localStorage for the list, IndexedDB for images), so there's no database and nothing of theirs on your server. The image cache is server-side and shared, so one download serves everybody.

### Image quality

`IMAGE_FORMAT` and `MAX_WIDTH` control how cached images are stored — the default is `webp-q85` at 1100px, enough for a 2x display at the widest the card ever renders, averaging ~140 KB an image. Originals are kept alongside in `cache/source`, so changing either setting only costs CPU:

```
docker exec -it coaster-ranker python3 /app/warm_cache.py --reencode
```

## Credits

- **[Claude-Pairwise-Coaster-Ranker](https://github.com/coasterrankerburner-gif/Claude-Pairwise-Coaster-Ranker)** by coasterrankerburner-gif, which this project is derived from
- **[RCDB](https://rcdb.com)**, the Roller Coaster DataBase, for the coaster data and photography, with Coasterpedia and Wikipedia as fallbacks
- **[Catppuccin](https://github.com/catppuccin)**, for the Latte (light) and Mocha (dark) color schemes
- **Bebas Neue** by Ryoichi Tsunekawa, **DM Sans** by the DM Sans Project Authors (Colophon Foundry), and **JetBrains Mono** by JetBrains, the typefaces, loaded from Google Fonts
- **React** by Meta and contributors, **Vite** by Evan You and contributors, **Lucide** icons by the Lucide contributors, **PapaParse** by Matt Holt and contributors, and **react-dropzone** by Param Aggarwal and contributors, for the interface and list importing
- **FastAPI** by Sebastián Ramírez, **Uvicorn** by Tom Christie and contributors, and **Pillow** by Jeffrey "Alex" Clark and contributors, for the image server

## License

Coaster Ranker is free software under the [GNU Affero General Public License, version 3](LICENSE).

---

*This project was made with AI assistance. For more information, see [my AI usage statement](https://github.com/cmyksoda/cmyksoda/blob/main/AI_USAGE.md).*
