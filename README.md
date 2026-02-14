# Automated X (Twitter) Account - Lithium Industry News Bot

An intelligent Twitter bot that automatically monitors lithium industry news via RSS feeds, uses AI to analyze and filter articles, and posts engaging tweets and threads. The bot intelligently prioritizes articles mentioning specific companies and creates contextually relevant content using Claude AI.

## 📊 Overview

This automation creates a fully autonomous Twitter account that:
- Monitors multiple RSS feeds for lithium industry news
- Uses AI to analyze articles for company mentions
- Prioritizes news about specific lithium companies
- Generates engaging single tweets and multi-part threads
- Posts automatically on a schedule
- Maintains conversation memory to avoid repetition
- Formats content optimally for Twitter

## ✨ Key Features

- **Automated News Discovery**: Monitors RSS feeds every 10 hours
- **AI-Powered Filtering**: Uses Claude to identify articles mentioning target companies
- **Smart Prioritization**: Articles with company mentions posted as threads, others as single tweets
- **Dual Content Strategy**: 
  - **Threads** for company-specific news (more detailed)
  - **Single Tweets** for general industry news
- **Conversation Memory**: Remembers recently tweeted content to ensure variety
- **Rate Limiting**: Built-in delays to respect Twitter API limits
- **Markdown Processing**: Converts HTML content to clean text
- **Batch Processing**: Handles multiple articles efficiently
- **Error Handling**: Gracefully handles API failures

## 🏗️ Workflow Architecture

### Two Parallel Paths:

#### **Path 1: Thread Generation** (Company-Specific News)
```
Schedule Trigger (Every 10 hours)
    ↓
RSS Feed Read (Fetch news)
    ↓
Filter Recent Articles
    ↓
Prepare for AI Analysis
    ↓
Pick Most Recent 5 Articles
    ↓
Loop Through Articles
    ↓
Analyze with Claude (Check for company mentions)
    ↓
Parse AI Response & Merge Data
    ↓
Filter: Has Company Mention? 
    ├── NO → Halt
    └── YES → Continue
        ↓
    Pick First Article
        ↓
    Convert HTML to Markdown
        ↓
    Wait 10 Seconds
        ↓
    ThreadBot (Claude AI)
        ├── Article Memory (context)
        ├── Create Top Tweet (AI tool)
        ├── Delay (50 seconds)
        └── Create Thread Tweets (AI tool)
```

#### **Path 2: Single Tweet Generation** (General News)
```
Schedule Trigger (Every 10 hours)
    ↓
RSS Feed Read (Different feed)
    ↓
Filter Recent Articles
    ↓
Prepare for AI Analysis
    ↓
Pick Most Recent 5 Articles
    ↓
Loop Through Articles
    ↓
Analyze with Claude (Check for company mentions)
    ↓
Parse AI Response & Merge Data
    ↓
Filter: Has Company Mention?
    ├── YES → Halt (handled by Thread path)
    └── NO → Continue
        ↓
    Pick First Article
        ↓
    Convert HTML to Markdown
        ↓
    Wait 10 Seconds
        ↓
    TweetBot (Claude AI)
        ├── Simple Memory (context)
        └── Tweet (AI tool)
```

## 📋 Prerequisites

### Required Accounts & Credentials

1. **n8n Instance** (Cloud or Self-hosted)
   - [Get started with n8n](https://n8n.io/)
   - Requires LangChain support

2. **X (Twitter) Developer Account** (Free tier available)
   - Apply at [Twitter Developer Portal](https://developer.twitter.com/)
   - Create app and get OAuth 2.0 credentials
   - **Required Scopes**: 
     - `tweet.read`
     - `tweet.write`
     - `users.read`
     - `offline.access`
   - Free tier: 1,500 tweets/month, 50,000 reads/month

3. **Anthropic Claude API** (Paid, with free trial)
   - Sign up at [Anthropic Console](https://console.anthropic.com/)
   - Get API key
   - This workflow uses Claude Sonnet for both analysis and generation
   - Pricing: ~$3 per million input tokens

4. **RSS Feeds** (Free)
   - Google News RSS (no auth needed)
   - Or any lithium industry news RSS feeds

### Technical Requirements

- n8n version 1.0.0 or higher with LangChain support
- Internet connection for API calls
- Understanding of Twitter content guidelines

## 🚀 Installation

### Step 1: Set Up Twitter Developer Account

1. **Apply for Developer Access**:
   - Go to [Twitter Developer Portal](https://developer.twitter.com/)
   - Click "Sign up" (if new)
   - Complete application (describe use case: "Automated news bot for lithium industry")
   - Wait for approval (usually instant for basic access)

2. **Create Twitter App**:
   - Go to Developer Portal → "Projects & Apps"
   - Create new app
   - Name: e.g., "Lithium News Bot"
   - Get your credentials:
     - API Key
     - API Secret
     - OAuth 2.0 Client ID
     - OAuth 2.0 Client Secret

3. **Configure App Settings**:
   - Set app permissions to "Read and Write"
   - Enable OAuth 2.0
   - Add callback URL (for n8n OAuth flow)
   - Save settings

### Step 2: Set Up Anthropic Claude API

1. **Create Account**:
   - Go to [Anthropic Console](https://console.anthropic.com/)
   - Sign up with email
   - Verify account

2. **Get API Key**:
   - Go to API Keys section
   - Click "Create Key"
   - Name it (e.g., "n8n Twitter Bot")
   - Copy the key (you won't see it again!)

3. **Add Credits** (if needed):
   - Free tier: $5 credit for testing
   - Add payment method for production use
   - Monitor usage in console

### Step 3: Import n8n Workflow

1. Download `Automated_X_Account.json`
2. Open n8n instance
3. Click **Workflows** → **Import from File**
4. Select the JSON file
5. Click **Import**

### Step 4: Configure Credentials

#### A. Twitter OAuth2 API

1. Click any **Tweet** node (e.g., "create_top_tweet")
2. Click **Credentials** → **Create New**
3. Select **Twitter OAuth2 API**
4. Enter your Twitter app credentials:
   - **OAuth2 Client ID**
   - **OAuth2 Client Secret**
5. Click **Connect** to authorize
6. Allow the app to access your Twitter account
7. Save credential

**Important**: Use the SAME credential for all Twitter nodes:
- "Tweet"
- "create_top_tweet"
- "create_thread_tweets"

#### B. Anthropic Claude API

1. Click **"Anthropic Chat Model"** node
2. Click **Credentials** → **Create New**
3. Select **Anthropic API**
4. Enter your API key
5. Test connection
6. Save

Use the same credential for:
- "Anthropic Chat Model"
- "Anthropic Chat Model1"

### Step 5: Configure RSS Feeds

#### For Thread Generation Path:

1. Click **"RSS Read"** node
2. Configure:
   - **URL**: Your RSS feed URL
   - Example: `https://news.google.com/rss/search?q=lithium+mining+when:7d&hl=en-US&gl=US&ceid=US:en`

#### For Single Tweet Path:

1. Click **"RSS Read1"** node
2. Configure different RSS feed or same with different parameters
3. Consider using different keywords for variety

**RSS Feed Tips**:
- Google News RSS format: `https://news.google.com/rss/search?q=YOUR_QUERY&when:7d`
- Replace spaces with `+` in query
- Use `when:Xd` for days (7d = last 7 days)
- Add language: `&hl=en-US`
- Add region: `&gl=US&ceid=US:en`

### Step 6: Customize Company Targeting

The workflow filters for specific lithium companies. To customize:

1. Find **"Analyse articles"** node (it's a Claude API node)
2. Edit the prompt to include your target companies
3. Look for this section:
   ```
   "mentioned_companies": ["Lithium Americas", "Albemarle", "SQM", "etc..."]
   ```
4. Replace with your target companies

Example companies to track:
- Lithium Americas
- Albemarle Corporation
- SQM (Sociedad Química y Minera)
- Livent Corporation
- Pilbara Minerals
- Allkem
- Mineral Resources
- Ganfeng Lithium

### Step 7: Adjust Posting Schedule

**Default**: Every 10 hours at :01 past the hour

To change:

1. Click **"Schedule Trigger"** node (for threads)
2. Modify:
   - `hoursInterval`: Hours between executions (1-24)
   - `triggerAtMinute`: Minute of the hour (0-59)

3. Click **"Schedule Trigger1"** node (for single tweets)
4. Adjust independently for different timing

**Recommended Schedules**:
- **High Frequency**: Every 6 hours (4 posts/day)
- **Moderate**: Every 8 hours (3 posts/day)
- **Low Frequency**: Every 12 hours (2 posts/day)
- **Daily**: Every 24 hours at specific time

**Best Times to Post** (EST):
- 8:00 AM (morning commute)
- 12:00 PM (lunch break)
- 5:00 PM (evening commute)
- 9:00 PM (evening browsing)

### Step 8: Test the Workflow

#### Test Thread Generation:
1. Click **"Schedule Trigger"** node
2. Click **"Execute Node"**
3. Watch execution flow
4. Check Twitter for posted thread

#### Test Single Tweet:
1. Click **"Schedule Trigger1"** node
2. Click **"Execute Node"**
3. Watch execution
4. Check Twitter for tweet

#### If Errors Occur:
- Check execution logs
- Verify credentials are correct
- Ensure RSS feeds are accessible
- Check Twitter API limits
- Review Claude API quota

### Step 9: Activate Workflow

1. Click the **Active** toggle in top-right corner
2. Workflow is now autonomous!
3. Monitor first few executions

## ⚙️ Configuration Options

### Customize Tweet Style

Edit **"ThreadBot"** node prompt to change thread style:

```
You are a professional lithium industry analyst creating Twitter threads.

Style guidelines:
- Use professional but engaging tone
- Include relevant statistics
- Add context for non-experts
- Use bullet points sparingly
- Keep tweets under 280 characters
- End threads with key takeaway

Format:
- First tweet: Hook + key point
- Middle tweets: Details + analysis
- Last tweet: Conclusion + call-to-action
```

Edit **"TweetBot"** node for single tweet style:

```
You are creating a single engaging tweet about lithium industry news.

Requirements:
- Maximum 280 characters
- Include key information
- Add relevant hashtags (2-3 max)
- Make it shareable
- Professional tone

Examples:
- "Breaking: [Company] announces new lithium discovery in [Location]. Production expected to [Impact]. #Lithium #Mining"
```

### Adjust Article Count

**Current**: Analyzes 5 most recent articles per execution

To change:

1. Find **"Pick most recent 5"** node
2. Edit limit parameter
3. Recommended: 3-10 articles

**Considerations**:
- More articles = more Claude API usage
- More articles = longer execution time
- Fewer articles = might miss important news

### Modify Memory Settings

**Article Memory** (Thread generation):
- Prevents re-posting about same articles
- Default: Last 5 processed articles

**Simple Memory** (Single tweets):
- Remembers recent tweets to avoid repetition
- Default: Last 4 tweets
- Edit in **"Simple Memory"** node: `contextWindowLength`

### Change Delays

**Between Thread Tweets**: 50 seconds (Twitter rate limit protection)
- Edit in **"delay"** tool node

**Before AI Generation**: 10 seconds (processing time)
- Edit in **"Wait"** and **"Wait1"** nodes

**Between Article Processing**: 5 seconds
- Edit in **"Wait 5 secs"** nodes

## 📝 Content Strategy

### Thread Generation (Company-Specific)

**When triggered**:
- Article mentions target company
- Usually significant news (earnings, deals, discoveries)
- Warrants detailed analysis

**Structure**:
1. **Tweet 1** (Top tweet): Hook + headline
2. **Tweet 2-4**: Key details, analysis, context
3. **Final Tweet**: Conclusion + implications

**Example Thread**:
```
🔋 BREAKING: Lithium Americas secures $2.3B DOE loan for Thacker Pass

This is the largest federal investment in domestic lithium production. Here's what it means for US supply chain resilience 🧵

1/ The Thacker Pass project in Nevada will produce 40,000 tons/year of lithium carbonate - enough for ~800,000 EVs annually...

2/ This marks a shift in US policy toward onshoring critical minerals. Previous reliance on imports from China/Australia creating supply chain vulnerabilities...

3/ Production start: 2027. At scale, this single site could supply 25% of US lithium demand. Major validation of domestic mining...

4/ Key takeaway: Federal backing signals long-term commitment to domestic battery supply chain. Watch for similar announcements in other critical minerals. 

#Lithium #Mining #EVs
```

### Single Tweet (General Industry News)

**When triggered**:
- Article doesn't mention target companies
- General industry trends
- Market updates
- Technology news

**Structure**:
- Single focused message
- Key stat or insight
- 2-3 relevant hashtags

**Example Tweets**:
```
Global lithium demand projected to triple by 2030 driven by EV adoption. Current production capacity falling 20% short of projections. Major supply crunch ahead. #Lithium #EVs

---

New lithium extraction technology reduces water usage by 90% compared to traditional methods. Game-changer for water-stressed mining regions. #Mining #Sustainability

---

China controls 77% of global lithium refining capacity. US and EU racing to build domestic processing capabilities. Geopolitical implications huge. #Lithium #SupplyChain
```

## 🔧 Advanced Customization

### Add Image Generation

Integrate with image generation APIs to create visual content:

1. Add DALL-E or Midjourney API node
2. Generate relevant images based on article topic
3. Attach to tweets using Twitter media upload

### Multi-Language Support

Post in multiple languages:

1. Add language detection to RSS content
2. Use Claude to translate content
3. Post to different Twitter accounts per language

### Sentiment Analysis

Track market sentiment:

1. Add sentiment analysis to AI prompt
2. Log sentiment scores to database
3. Create weekly sentiment reports

### Competitive Monitoring

Track competitor mentions:

1. Add RSS feeds for competing accounts
2. Analyze their posting strategy
3. Identify trending topics they miss
4. Fill content gaps

### Analytics Dashboard

Track performance:

1. Store tweet IDs in database
2. Fetch engagement metrics daily
3. Create dashboard in Google Sheets
4. Optimize posting times based on data

## 🎯 Best Practices

### Content Quality

1. **Verify Information**: AI can hallucinate - always fact-check
2. **Add Value**: Don't just regurgitate headlines
3. **Be Timely**: Post breaking news quickly
4. **Stay Relevant**: Focus on your niche (lithium industry)
5. **Engage Ethically**: Disclose it's an automated account (in bio)

### Twitter Guidelines

1. **Avoid Spam**: Don't post too frequently
2. **No Manipulation**: Don't artificially inflate engagement
3. **Authentic Content**: Provide real value
4. **Respect Copyright**: Link to original sources
5. **Follow Rules**: Review Twitter Automation Rules

### Technical Maintenance

1. **Monitor Logs**: Check for errors weekly
2. **Update Feeds**: RSS feeds can break - have backups
3. **Rotate API Keys**: Security best practice
4. **Test Changes**: Always test in staging first
5. **Backup Workflow**: Export JSON regularly

### API Quota Management

**Twitter Free Tier**:
- 1,500 tweets/month = ~50 tweets/day
- This workflow: ~5 posts/day maximum (well within limits)

**Claude API**:
- Monitor usage daily
- Set billing alerts
- Optimize prompts to reduce token usage
- Consider caching for repeated queries

## ⚠️ Important Notes

### Legal & Ethical Considerations

**Disclosure**:
- Add "Automated account" to Twitter bio
- Mention it's AI-powered
- Link to human maintainer

**Copyright**:
- Always link to original articles
- Don't copy full text
- Use excerpts within fair use

**Accuracy**:
- AI can make mistakes
- Implement human oversight
- Correct errors quickly

**Liability**:
- You're responsible for bot's posts
- Monitor for inappropriate content
- Have kill switch ready

### Twitter Automation Policy

Twitter allows automation if:
- ✅ You control the account
- ✅ Content adds value
- ✅ Not misleading or manipulative
- ✅ Respects rate limits
- ✅ Doesn't spam or harass

Twitter prohibits:
- ❌ Bulk/aggressive following
- ❌ Posting identical content across accounts
- ❌ Manipulating trends
- ❌ Fake engagement schemes

### Rate Limits & Costs

**Twitter API Free Tier**:
- 1,500 tweets/month
- 50,000 tweet reads/month
- This workflow: ~150 tweets/month (10% of limit)

**Claude API Costs**:
- Analysis: ~500 tokens/article
- Thread generation: ~2,000 tokens
- Single tweet: ~500 tokens
- **Monthly estimate**: ~$15-30 for daily posting

**RSS Feeds**:
- Free, no limits
- Ensure reliable sources

## 🐛 Troubleshooting

### No Tweets Posted

**Symptom**: Workflow executes but nothing on Twitter

**Solutions**:
1. Check RSS feed has new articles
2. Verify articles meet filtering criteria (company mentions)
3. Check Twitter credentials haven't expired
4. Review execution logs for errors
5. Test with manual execution

### AI Analysis Failures

**Symptom**: All articles filtered out or parsing errors

**Solutions**:
1. Check Claude API key is valid
2. Verify prompt format is correct
3. Review company names in filter list
4. Check article content has enough detail
5. Increase context length for longer articles

### Duplicate Posts

**Symptom**: Same content posted multiple times

**Solutions**:
1. Check memory nodes are configured correctly
2. Verify session keys are unique
3. Increase memory window size
4. Add deduplication logic

### Rate Limit Errors

**Symptom**: "429 Too Many Requests" errors

**Solutions**:
1. Increase delays between tweets (currently 50 seconds)
2. Reduce posting frequency
3. Upgrade Twitter API tier
4. Implement exponential backoff

### Poor Quality Tweets

**Symptom**: Tweets are generic or off-topic

**Solutions**:
1. Improve AI prompts with examples
2. Add more context to articles
3. Increase article quality by filtering RSS feeds
4. Add fact-checking step
5. Use higher quality model (Claude Opus)

## 🔒 Security Best Practices

- **Never commit API keys** to repositories
- **Use n8n credential system** (encrypted)
- **Rotate keys quarterly**
- **Monitor account for unauthorized access**
- **Enable 2FA on Twitter account**
- **Set spending limits on Claude API**
- **Review posted content daily**
- **Have incident response plan**

## 💡 Pro Tips

1. **Prime Posting Times**: Schedule around US market hours (9 AM - 4 PM EST)
2. **Hashtag Strategy**: Use 2-3 relevant hashtags max
3. **Thread Length**: Optimal is 3-5 tweets per thread
4. **Engagement**: Manually engage with replies to boost visibility
5. **Variety**: Mix company-specific and general industry content
6. **Consistency**: Post regularly (daily ideal)
7. **Quality Over Quantity**: Better to post less frequently with higher quality
8. **Monitor Competitors**: Learn from successful lithium industry accounts
9. **A/B Testing**: Try different posting times and styles
10. **Stay Current**: Update RSS feeds as new sources emerge

## 📈 Measuring Success

Track these metrics:

**Engagement**:
- Impressions per tweet
- Engagement rate (likes + retweets + replies / impressions)
- Profile visits
- Follower growth rate

**Content Performance**:
- Best performing topics
- Thread vs single tweet performance
- Optimal posting times
- Hashtag effectiveness

**API Efficiency**:
- Claude tokens per tweet
- Cost per engagement
- RSS feed article quality
- Filtering accuracy

**Goals** (3-month targets):
- 500+ followers
- 5%+ engagement rate
- 10K+ monthly impressions
- Recognized as industry resource

## 🤝 Contributing

Ideas for improvements:
- Add image generation for visual content
- Implement engagement tracking
- Create analytics dashboard
- Add reply automation (with human review)
- Multi-account support
- Video clip generation from articles
- Integrate with Discord for notifications
- Add A/B testing framework

## 📝 License

This workflow is provided as-is for educational and commercial use. Comply with Twitter's automation policies and terms of service.

## 📞 Support

For issues with:
- **n8n**: [n8n Community Forum](https://community.n8n.io/)
- **Twitter API**: [Twitter Developer Documentation](https://developer.twitter.com/en/docs)
- **Anthropic**: [Anthropic Documentation](https://docs.anthropic.com/)

## ⚖️ Disclaimer

**Important**: This automation is for educational purposes. Users must:
- Comply with Twitter's Terms of Service
- Follow Twitter Automation Rules
- Ensure content accuracy
- Respect intellectual property
- Monitor and maintain the bot

The creator assumes no liability for misuse or policy violations.

---

**Built with ❤️ using n8n, Claude AI, and the Twitter API**

*Last Updated: February 2026*
