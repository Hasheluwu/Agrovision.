# 🌾 AgroVision DEMO

**Link:** [https://agrovision10.vercel.app/](https://agrovision10.vercel.app/)

**AgroVision DEMO** is a personal project that started as a university assignment. It attempts to propose a solution to a real problem in Nicaragua: **crop loss caused by misinformation or difficulty accessing useful agricultural information**. The traditional methods Nicaraguan farmers use are gradually becoming less accurate due to global warming, and the rise of artificial intelligence opens up new and interesting possibilities.

## What is it?

AgroVision is a **free demo** that aims to help small and medium-scale producers in Nicaragua decide **what crop to plant, when, and with which inputs** — by simulating the future climate of their area and calculating whether it's worth it or not, in real córdobas.

In general terms, AgroVision is an **expert system** that lets you "simulate" having a farm. You have your supplies, your available crops to plant, and your plots with their respective active or passive tools. A passive tool would be a specific mesh netting, and an active one would be an irrigation system that activates when needed. You also define your plot's soil type, terrain slope, the year you want to plant, and the specific municipality — **we have all of them in Nicaragua**.

The system has information on each available crop: its growth phases, when planting begins in Nicaragua's 3 main agricultural cycles (**primera, postrera, apante**), when each cycle ends, water requirements in mm per phase, and most importantly, **what climate conditions are ideal for that crop**. With this, the system knows what climate conditions to expect for each future day in your area.

With all that information, the system simulates what would happen if you planted a certain crop on that plot: which losses are unavoidable due to climate, which are avoidable if you have certain tools or supplies, and finally gives you the result in **money generated, quintals produced, and much more**. You can even change the sale price per quintal if you want to explore hypothetical scenarios.

## How did we build it?

First comes the **NASA data**. Using machine learning — essentially specialized math applied to computers to find patterns in phenomena — I obtained daily climate data in **50×50 kilometer grids** covering every part of Nicaragua.

Being 50×50 km grids, the information is moderately precise. For comparison: weather apps on your phone typically use 20×20 km grids for rainfall, which allows them to predict rain hours in advance. AgroVision's data is more general for now, which works fine for some variables but could improve for others like rainfall, depending on data and resources we obtain in the future. That said, we do provide more precise solutions for variables that require it, like soil moisture at the root level.

**The variables obtained from NASA are:**

- **PRECTOTCORR:** Rainfall (mm per day)
- **T2M_MAX / T2M_MIN / T2M:** Maximum, Minimum and Average Temperature (°C)
- **WS2M:** Wind speed (m/s)
- **RH2M:** Relative Humidity (%)
- **ALLSKY_SFC_PAR_TOT:** Photosynthetically Active Radiation (W/m²)
- **ALLSKY_SFC_SW_DIFF:** Diffuse Radiation (W/m²)
- **GWETTOP / GWETROOT / GWETPROF:** Surface, Root Zone and Deep Soil Moisture (fraction 0 to 1)
- **T2MDEW:** Dew Point (°C)
- **TS:** Soil Temperature (°C)
- **BRECHA_ROCIO:** Dew Gap, calculated as T2M_MIN minus T2MDEW (°C)

This data was collected daily from **2010 to 2025**. Then, using machine learning, I trained a model that learns the mathematical patterns of each variable and uses them to predict future years. We now have these variables projected for **2026–2029**. In Nicaragua these variables are especially erratic, which makes this problem particularly interesting.

In the graphics section of the site you can see how those predictions turn out. The model successfully captures general patterns, but extreme events like very strong storms or specific natural phenomena can't be detected realistically with this approach — that requires different data, engineering, and resources. The government already handles this with specialized techniques and a different approach, focused on informing days or weeks in advance.

## The pillars of the simulation engine

Even though I'm not an agronomist, the system is built on **real scientific principles**:

1.  **Yield Gap Analysis** — The plant starts with the potential to produce 100% of its harvest. The system assumes that maximum from the start and subtracts percentages when climate causes losses that couldn't be countered. It's more realistic for predicting damage than trying to "add up" growth day by day.

2.  **Stateful Bucket Model** — The soil works like a sponge with two layers: the surface, which fills quickly with rain but evaporates fast, and the root zone, which fills more slowly but retains water longer. If it poured on Tuesday, the sponge is full. If Thursday and Friday are sunny with no rain, the system doesn't cry "drought!" — it checks its virtual sponge and says "relax, the roots still have water from Tuesday." This replaces generic NASA data with a **plot-specific microclimate** for variables that directly affect the plant.

3.  **Phenological Thresholds** — Climate doesn't affect a newly germinated plant the same way it affects one in flowering. The engine evaluates each climate event according to the exact growth phase of the crop.

4.  **Strict Climate Synergy** — Pests and fungi don't appear out of nowhere — they need the exact combination of conditions. For example: *"If humidity is >85% AND temperature is <22°C AND there is physical dew... the fungus appears."* There are also events that only trigger if those conditions persist for several consecutive days.

5.  **Mitigation Cost-Benefit Logic** — When the system detects a threat, it calculates how much money you'd lose on the harvest and how much it would cost to mitigate it (including equipment amortization, fuel, labor, or input price). If saving the plant costs 1,000 córdobas but the loss was only 500, the system says: *"Not worth it, take the loss."* If it's the other way around, it activates the solution and saves the crop.

## Meet ARI: the AI with the keys to the engine

To make this more than a glorified chatbot giving generic advice, I programmed **ARI**, the system's artificial intelligence assistant. ARI has direct access to the simulation engine. You speak to her in natural language and she decides which of these **6 tools** to run to give you a real mathematical and financial answer:

*   **Individual Simulation with Time Window** — Evaluates one crop on one plot. In window mode, it travels days forward or backward simulating multiple planting dates and returns only the most profitable one.
*   **1 Crop vs. Multiple Plots** — Takes your desired crop and pits it against all your plots simultaneously, delivering a ranking by ROI.
*   **Multiple Crops vs. 1 Plot** — The reverse: simulates planting corn, beans, tomatoes, etc. on a single plot and eliminates those the climate would destroy, ordering survivors by net profit.
*   **Mass Extraction** — Runs independent simulations of every possible combination of your plots and crops, ideal for a quick overview of the full season.
*   **Shared Farm Simulation** — The most realistic. Simulates your entire farm in cascade sharing a single warehouse. If the beans on Plot 1 consume all the fertilizer, the corn on Plot 2 will suffer the consequences. At the end it delivers the global ROI of your entire operation.
*   **Market Modifier** — Changes the sale price or planting cost of any crop to explore hypothetical scenarios before making decisions.

## On cost and limitations

The program is **completely free** and currently generates no revenue. That's why I'm using a fairly affordable conversational AI model — each user has a limit of 20 messages per day and the AI has no memory of previous chats, it only reads the current message. If there were ever ads, I could use more capable models with more context and fewer occasional errors.

The crop data, while based on national and international sources and real scientific systems, was compiled by someone who is not an agronomist. The system assumes the user already knows how to prepare their land and plant — what AgroVision adds is information about what can't be known alone, like future climate predictions and their economic impact on the specific crop.

## Looking ahead

I genuinely like this project and believe it could be useful in Nicaragua — I haven't seen anything similar here. Updates will come weekly or every two weeks. The next priority is making passive tools dynamic (having the system tell you when to install and remove them mid-execution, enabling more complex crops like coffee) and finding collaborators from the agronomy field.

---

## 🎮 Other Projects

**Hunted** — A first-person survival horror game built from scratch in C++ and OpenGL 3.3, featuring custom A* pathfinding, skeletal animation, dynamic lighting, spatial audio, and a full gameplay loop with keys, puzzles, and a state-machine-driven monster AI.

🔗 [github.com/Hasheluwu/ProgramacionGraficaJuegoTerror](https://github.com/Hasheluwu/ProgramacionGraficaJuegoTerror)

---

My contact info and the demo link are below if anything here caught your attention.

*   **Reddit:** [u/Less_Measurement8733](https://reddit.com/u/Less_Measurement8733)
*   **Twitter/X:** [https://x.com/Der_114](https://x.com/Der_114)
*   **AgroVision Link:** [https://agrovision10.vercel.app/](https://agrovision10.vercel.app/)
