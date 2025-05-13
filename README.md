<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Rise of Shady: The Quest for Greatness</title>
    <style>
        /* Global Styles */
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: linear-gradient(to bottom, #111827, #1f2937);
            color: #ffffff;
            margin: 0;
            padding: 1rem;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        .container {
            width: 90%;
            max-width: 768px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        section {
            margin-bottom: 1.5rem;
            width: 100%;
        }

        h1 {
            color: #facc15;
            font-size: 1.875rem;
            font-weight: 700;
            text-align: center;
            margin-bottom: 1rem;
        }

        h2 {
            color: #fde68a;
            font-size: 1.25rem;
            font-weight: 600;
            margin-bottom: 0.5rem;
        }

        h3 {
            font-size: 1.125rem;
            font-weight: 600;
            color: #ffffff;
            margin-top: 1rem;
            margin-bottom: 0.25rem;
            text-transform: uppercase;
        }

        p {
            font-size: 1.125rem;
            line-height: 1.5;
            margin-bottom: 0.5rem;
        }

        .card {
            background-color: #374151;
            border-radius: 0.375rem;
            padding: 1rem;
            margin-bottom: 0.5rem;
            color: #ffffff;
        }

        .grid-container {
            display: grid;
            grid-template-columns: 1fr;
            gap: 1rem;
        }

        @media (min-width: 640px) {
            .grid-container {
                grid-template-columns: repeat(2, 1fr);
            }
        }

        .progress-bar-container {
            background-color: #374151;
            border-radius: 0.25rem;
            height: 0.5rem;
            width: 16rem;
            margin-top: 0.5rem;
        }

        .progress-bar-fill {
            background-color: #22c55e;
            height: 0.5rem;
            border-radius: 0.25rem;
            width: 0%; /* Will be updated by JS */
        }

        .quest-card {
            background-color: #4b5563;
            border-radius: 0.375rem;
            padding: 0.5rem;
            margin-bottom: 0.5rem;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .quest-details {
            flex-grow: 1;
            margin-right: 1rem;
        }

        .quest-button {
            background-color: #eab308;
            color: #000000;
            border: none;
            padding: 0.75rem 1rem;
            border-radius: 0.25rem;
            cursor: pointer;
            font-size: 1rem;
        }

        .quest-button:hover {
            background-color: #ca8a04;
        }

        .reward-card {
            background-color: #374151;
            border-radius: 0.375rem;
            padding: 1rem;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .reward-details {
            flex-grow: 1;
            margin-right: 1rem;
        }

        .reward-button {
            background-color: #22c55e;
            color: #ffffff;
            border: none;
            padding: 0.75rem 1rem;
            border-radius: 0.25rem;
            cursor: pointer;
            font-size: 1rem;
        }

        .reward-button:hover {
            background-color: #16a34a;
        }

        .reward-owned {
            background-color: #6b7280;
            color: #ffffff;
            border: none;
            padding: 0.75rem 1rem;
            border-radius: 0.25rem;
            font-size: 1rem;
            cursor: default;
        }

        .save-button {
            background-color: #3b82f6;
            color: #ffffff;
            border: none;
            padding: 1.5rem 0.5rem;
            border-radius: 0.25rem;
            cursor: pointer;
            font-size: 1rem;
            width: 100%;
            max-width: 200px;
        }

        .save-button:hover {
            background-color: #2563eb;
        }

        .error-message {
            color: #f87171;
            font-size: 1.25rem;
            text-align: center;
            margin-top: 1rem;
        }
    </style>
</head>
<body>
    <div class="container">
        <section>
            <h1>Rise of Shady: The Quest for Greatness</h1>
        </section>

        <section>
            <p id="player-stats">Level: 1 | Shady Coins: 0</p>
            <p>Freedom Meter: <span id="freedom-meter-value">0</span>/10</p>
            <div class="progress-bar-container">
                <div class="progress-bar-fill" id="freedom-meter-bar"></div>
            </div>
        </section>

        <section>
            <h2>Stat Tracker</h2>
            <div class="grid-container" id="stat-tracker">
                </div>
        </section>

        <section>
            <h2>Quest Log</h2>
            <div id="quest-log">
                </div>
        </section>

        <section>
            <h2>Reward Shop</h2>
            <div class="grid-container" id="reward-shop">
                </div>
        </section>

        <section>
            <button class="save-button" onclick="saveProgress()">Save Progress</button>
        </section>

        <p id="error-display" class="error-message" style="display: none;"></p>
    </div>

    <script>
        // --- Data ---
        const initialStats = {
            medicalMastery: { xp: 0 },
            russianEdge: { xp: 0 },
            physicalPower: { xp: 0 },
            contentHustle: { xp: 0 },
            problemSolvingProwess: { xp: 0 },
            socialSwagger: { xp: 0 },
        };

        const initialCoins = 0;
        const initialFreedomMeter = 0;
        const initialPurchasedRewards = [];

        const quests = {
            daily: [
                { name: "Watch 15 min of doctor's video + 5 UWorld questions", rewards: { medicalMastery: { xp: 15, coins: 10 } } },
                { name: "15 min workout", rewards: { physicalPower: { xp: 10, coins: 5 } } },
                { name: "Journal 1 sentence", rewards: { problemSolvingProwess: { xp: 5, coins: 5 } } },
                { name: "Read 1 page in a Russian book", rewards: { russianEdge: { xp: 10, coins: 5 } } },
                { name: "Brainstorm 3 content ideas", rewards: { contentHustle: { xp: 10, coins: 5 } } },
                { name: "Reach out to one friend", rewards: { socialSwagger: { xp: 5, coins: 10 } } },
            ],
            weekly: [
                { name: "Post 1 TikTok", rewards: { contentHustle: { xp: 30, coins: 25 } } },
                { name: "Learn 10 Russian words", rewards: { russianEdge: { xp: 25, coins: 20 } } },
                { name: "3 x 30 min workouts", rewards: { physicalPower: { xp: 30, coins: 20 } } },
                { name: "Solve 5 complex problems", rewards: { problemSolvingProwess: { xp: 35, coins: 25 } } },
                { name: "Review 50 UWorld questions", rewards: { medicalMastery: { xp: 40, coins: 30 } } },
                { name: "Have a meaningful conversation with a family member", rewards: { socialSwagger: { xp: 30, coins: 20 } } },
            ],
            monthly: [
                { name: "4 workouts/week for 4 weeks", rewards: { physicalPower: { xp: 100, coins: 75 } } },
                { name: "Complete 500 UWorld questions", rewards: { medicalMastery: { xp: 120, coins: 100 } } },
                { name: "Have 3 Russian conversations", rewards: { russianEdge: { xp: 90, coins: 80 } } },
                { name: "Publish 4 pieces of content", rewards: { contentHustle: { xp: 110, coins: 90 } } },
                { name: "Finish a small personal project", rewards: { problemSolvingProwess: { xp: 100, coins: 85 } } },
                { name: "Attend 2 social events", rewards: { socialSwagger: { xp: 95, coins: 70 } } },
            ],
            boss: [
                { name: "Make USMLE study plan", rewards: { medicalMastery: { xp: 150, coins: 120 } } },
                { name: "5 min Russian conversation with a native speaker", rewards: { russianEdge: { xp: 130, coins: 110 } } },
                { name: "Run a 5k", rewards: { physicalPower: { xp: 140, coins: 100 } } },
                { name: "Launch a small online tool", rewards: { problemSolvingProwess: { xp: 160, coins: 130 } } },
                { name: "Grow social media following by 50", rewards: { socialSwagger: { xp: 125, coins: 115 } } },
                { name: "Create a month's worth of content", rewards: { contentHustle: { xp: 155, coins: 125 } } },
            ],
        };

        const rewardsData = [
            { id: "playlist", name: "Personalized Playlist", cost: 25, type: "Freedom" },
            { id: "book", name: "Interesting Book", cost: 40, type: "Tool" },
            { id: "outfit", name: "New Outfit", cost: 60, type: "Status" },
            { id: "headphones", name: "Premium Headphones", cost: 80, type: "Gear" },
            { id: "coffee", name: "Specialty Coffee", cost: 30, type: "Freedom" },
            { id: "notebook", name: "High-Quality Notebook", cost: 45, type: "Tool" },
            { id: "massage", name: "Relaxing Massage", cost: 120, type: "Freedom" },
            { id: "course", name: "Online Course Access", cost: 150, type: "Tool" },
            { id: "gadget", name: "Cool Gadget", cost: 100, type: "Gear" },
            { id: "trip", name: "Day Trip", cost: 130, type: "Freedom" },
        ];

        // --- State Management ---
        let stats = loadState('shadyStats', initialStats);
        let shadyCoins = loadState('shadyCoins', initialCoins);
        let freedomMeter = loadState('freedomMeter', initialFreedomMeter);
        let purchasedRewards = loadState('purchasedRewards', initialPurchasedRewards);

        function loadState(key, defaultValue) {
            try {
                const storedState = localStorage.getItem(key);
                return storedState ? JSON.parse(storedState) : defaultValue;
            } catch (error) {
                console.error(`Error loading ${key} from local storage:`, error);
                displayError(`Error loading saved data. Try refreshing or clearing cache.`);
                return defaultValue;
            }
        }

        function saveState(key, value) {
            try {
                localStorage.setItem(key, JSON.stringify(value));
            } catch (error) {
                console.error(`Error saving ${key} to local storage:`, error);
                displayError(`Error saving progress. Please try again.`);
            }
        }

        function calculateLevel(xp) {
            return Math.floor(xp / 100) + 1;
        }

        function calculateRank(xp) {
            if (xp <= 300) return "Bronze";
            if (xp <= 600) return "Silver";
            if (xp <= 1000) return "Gold";
            if (xp <= 1500) return "Platinum";
            return "Grand Champion";
        }

        function updatePlayerStatsDisplay() {
            const totalXP = Object.values(stats).reduce((sum, stat) => sum + stat.xp, 0);
            const level = Math.floor(totalXP / 1000) + 1;
            document.getElementById('player-stats').textContent = `Level: ${level} | Shady Coins: ${shadyCoins}`;
            document.getElementById('freedom-meter-value').textContent = freedomMeter;
            document.getElementById('freedom-meter-bar').style.width = `${freedomMeter * 10}%`;
        }

        function displayError(message) {
            const errorDisplay = document.getElementById('error-display');
            errorDisplay.textContent = message;
            errorDisplay.style.display = 'block';
        }

        // --- Render Components ---
        const statTrackerDiv = document.getElementById('stat-tracker');
        for (const statName in stats) {
            const stat = stats[statName];
            const level = calculateLevel(stat.xp);
            const rank = calculateRank(stat.xp);
            const card = document.createElement('div');
            card.classList.add('card');
            card.innerHTML = `
                <h3>${statName.replace(/([A-Z])/g, ' $1').trim()}</h3>
                <p>XP: ${stat.xp} | Level: ${level}</p>
                <p>Rank: ${rank}</p>
            `;
            statTrackerDiv.appendChild(card);
        }

        const questLogDiv = document.getElementById('quest-log');
        for (const category in quests) {
            const categoryDiv = document.createElement('div');
            const categoryHeader = document.createElement('h3');
            categoryHeader.textContent = `${category.charAt(0).toUpperCase() + category.slice(1)} Quests`;
            categoryDiv.appendChild(categoryHeader);

            quests[category].forEach((quest, index) => {
                const questCard = document.createElement('div');
                questCard.classList.add('quest-card');
                let rewardsText = '';
                for (const stat in quest.rewards) {
                    rewardsText += `+<span class="math-inline">\{quest\.rewards\[stat\]\.xp\} XP \(</span>{stat.replace(/([A-Z])/g, ' <span class="math-inline">1'\)\.trim\(\)\}\), \+</span>{quest.rewards[stat].coins} SC`;
                }
                questCard.innerHTML = `
                    <div class="quest-details">
                        <p>${quest.name}</p>
                        <p>Rewards: <span class="math-inline">\{rewardsText\}</p\>
</div\>
<button class\="quest\-button" onclick\="completeQuest\('</span>{category}', ${index})">Complete</button>
                `;
                categoryDiv.appendChild(questCard);
            });
            questLogDiv.appendChild(categoryDiv);
        }

        const rewardShopDiv = document.getElementById('reward-shop');
        rewardsData.forEach(reward => {
            const reward
