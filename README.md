# Ktemia skills for Claude Code, Codex, and Cursor

Agent skills that search and compare flights, rental cars, and hotels with the [Ktemia](https://www.ktemia.com/) [API](https://www.ktemia.com/docs/api/). Ask in plain language. The agent looks up the place, runs the search, and returns each seller's price with a booking link.

| Skill           | Use it for                                | Reference                                                  |
| --------------- | ----------------------------------------- | ---------------------------------------------------------- |
| `flight-search` | One-way, round trip, and multi-city fares | [Flights](https://www.ktemia.com/docs/api/flights/search/) |
| `car-search`    | A hire at a city or airport               | [Cars](https://www.ktemia.com/docs/api/cars/search/)       |
| `hotel-search`  | A stay, plus guest reviews                | [Hotels](https://www.ktemia.com/docs/api/hotels/search/)   |

Each skill asks when a place or a date is missing, instead of guessing. Cars and hotels can also be narrowed to named booking sites, and cars to named rental agencies. Ktemia compares prices. It does not book the ticket, the car, or the room. The traveller books on the seller's site.

## Install

Works in **Claude Code**, **Codex**, and **Cursor**. All three read the same `SKILL.md` format. One plugin install loads all three skills. Copy a single folder when you only want one of them.

### Claude Code

As a plugin, from inside Claude Code:

```
/plugin marketplace add Ktemiaa/ktemia-skills
/plugin install ktemia@ktemia
```

The old repository name still redirects, so an existing `Ktemiaa/ktemia-flights-skill` marketplace entry keeps resolving.

Or as standalone skills:

```bash
git clone --depth 1 https://github.com/Ktemiaa/ktemia-skills.git
mkdir -p ~/.claude/skills
cp -r ktemia-skills/skills/flight-search ~/.claude/skills/
cp -r ktemia-skills/skills/car-search ~/.claude/skills/
cp -r ktemia-skills/skills/hotel-search ~/.claude/skills/
```

### Codex

Codex loads skills from `~/.agents/skills/` (every project) or `.agents/skills/` in a repository:

```bash
git clone --depth 1 https://github.com/Ktemiaa/ktemia-skills.git
mkdir -p ~/.agents/skills
cp -r ktemia-skills/skills/flight-search ~/.agents/skills/
cp -r ktemia-skills/skills/car-search ~/.agents/skills/
cp -r ktemia-skills/skills/hotel-search ~/.agents/skills/
```

Restart Codex, then ask for a flight, a car, or a hotel. The skills call `api.ktemia.com`, so Codex may ask you to allow network access for those commands.

### Cursor

Cursor loads skills from `~/.cursor/skills/` or `~/.agents/skills/` (every project), or `.cursor/skills/` in a project:

```bash
git clone --depth 1 https://github.com/Ktemiaa/ktemia-skills.git
mkdir -p ~/.cursor/skills
cp -r ktemia-skills/skills/flight-search ~/.cursor/skills/
cp -r ktemia-skills/skills/car-search ~/.cursor/skills/
cp -r ktemia-skills/skills/hotel-search ~/.cursor/skills/
```

The skills appear under **Customize → Skills**. Use them from Agent chat, or type `/flight-search`, `/car-search`, or `/hotel-search`.

If you already installed them in `~/.agents/skills/` for Codex, Cursor finds them there too.

### One project only

To share the skills with everyone working in a repository, copy the folders into `.agents/skills/` in that repository and commit them. Codex and Cursor read that folder; for Claude Code, use `.claude/skills/`.

## Set up your API key

Every skill reads the same key from `KTEMIA_API_KEY`:

```bash
export KTEMIA_API_KEY="your-key"
```

How to get a key, and the plan limits, are on [authentication](https://www.ktemia.com/docs/api/#authentication) and [pricing](https://www.ktemia.com/pricing/). Every API call counts as one request: a search, each results page, each details call, and each page of hotel reviews.

## Try it

> Find flights from Lisbon to London on 15 March 2027.

> Hire a car at Lisbon airport, 10–17 March, automatic.

> Hire a car in Austin next month, but only from Avis or Hertz.

> A hotel in London for two, 10–12 March, with free cancellation if you can find it. Then show recent reviews for the one I pick.

> A London hotel for that weekend, priced on Booking.com only.

## Use the clients directly

Each skill bundles a small Python client with no dependencies:

```bash
python3 skills/flight-search/scripts/ktemia.py airports "Lisbon"
python3 skills/flight-search/scripts/ktemia.py search --origin LIS --destination LHR --date 2027-03-15

python3 skills/car-search/scripts/ktemia.py locations "Lisbon" --type airport
python3 skills/car-search/scripts/ktemia.py search --pickup LOCATION_ID --pickup-date 2027-03-10 --dropoff-date 2027-03-17
python3 skills/car-search/scripts/ktemia.py search --pickup LOCATION_ID --pickup-date 2027-03-10 --dropoff-date 2027-03-17 --agencies avis,hertz

python3 skills/hotel-search/scripts/ktemia.py locations "London"
python3 skills/hotel-search/scripts/ktemia.py search --location LOCATION_ID --checkin 2027-03-10 --checkout 2027-03-12
python3 skills/hotel-search/scripts/ktemia.py search --location LOCATION_ID --checkin 2027-03-10 --checkout 2027-03-12 --providers "Booking.com"
python3 skills/hotel-search/scripts/ktemia.py reviews HOTEL_ID
```

`results` and `details` take the `searchToken` the search command prints. Pass a hotel id to `reviews`; that call does not need a token.

`--providers` (cars and hotels) and `--agencies` (cars) keep only the sellers you name, up to 10 each. The ids come from `bookingSites` and `agencies` on a results page and are matched exactly, so read them from a first search rather than hard-coding them — booking-site ids carry capitals and dots (`Booking.com`), car agency ids are lowercase (`avis`).

## Learn more

- [API reference](https://www.ktemia.com/docs/api/) - every field and error, with code in cURL, Python, Node, Go, Java, Ruby, and PHP
- [Flights](https://www.ktemia.com/docs/api/flights/search/) · [Cars](https://www.ktemia.com/docs/api/cars/search/) · [Hotels](https://www.ktemia.com/docs/api/hotels/search/)
- [Flights in Cursor and Claude: MCP, skills, and instructions](https://www.ktemia.com/blog/flights-mcp/)

## About Ktemia

[Ktemia](https://www.ktemia.com/) is a search and compare platform for flights, hotels, and cars, with homes to follow. Use it in the [dashboard](https://www.ktemia.com/product/), through the [API](https://www.ktemia.com/docs/api/), or with the AI copilot. More guides are on the [blog](https://www.ktemia.com/blog/).

## License

MIT
