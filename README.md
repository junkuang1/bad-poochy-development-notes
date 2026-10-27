Bad Poochy: A comedy club for dogs who never asked for it.

Bad Poochy is my first app and my first time building a complete consumer product as a solo developer. It is now available on iOS and Android.



What I built:
Bad Poochy turns an ordinary dog photo into a designed comedic verdict. The user uploads a photo, crops it, chooses one of ten recurring judges, and receives a roast written from that judge's specific point of view. The result is presented as a branded verdict card that combines the dog photo, the judge, the roast, and Bad Poochy's visual identity. The card can then be saved or shared. The important part is that the judges are not simply ten names placed on top of the same joke generator. Each one has a different worldview, vocabulary, structure, and reason for being offended by the dog.

The current app includes:
One free roast and one default judge
Ten unlockable recurring judges
Credit-based access to additional roasts
One-time purchase packs for judge access and roast credits
Designed result cards that can be saved and shared
Account options through Apple, Google, email, or continued use on the device
Purchase restoration and in-app account deletion
Native support for iPhone, iPad, Android phones, and Android tablets

Why I built it:
I come from filmmaking, directing, and story development rather than a traditional software background. I became interested in whether a simple dog photo could become the starting point for character-driven comedy. Dogs already appear to have motives, grudges, confidence, shame, and terrible judgment. Their owners naturally invent stories around a stare, a sleeping position, a muddy paw, or a tongue pressed against a window. I wanted to build a product around that instinct. The idea was not merely to generate an insult. I wanted the uploaded photo to function as evidence and the selected judge to provide the point of view. The same dog should be able to receive completely different verdicts because a football manager, an HR investigator, an HOA president, and an angry chef do not interpret the same behavior in the same way.
That became the central idea behind Bad Poochy: one dog photo, ten judges, and ten different ways to misunderstand the evidence.

How Bad Poochy works: 
The user selects a dog photo through the native image picker. 
A custom crop screen allows the photo to be repositioned and resized in square or 4:3 format.
The user chooses a judge.
The app sends the image and that judge's instructions to a Supabase Edge Function.
The Edge Function verifies the user and available roast credits before requesting the analysis.
OpenAI analyzes the image using the selected judge's visual rules, character voice, comedy structure, and output format.
The response returns as structured JSON so the app can reliably place the headline, roast, statistics, verdict, or other judge-specific fields into the correct card design.
The completed verdict card can be saved to the device or shared through the phone's native sharing tools.
If the image does not contain a dog, the backend returns a specific no-dog result rather than pretending that it found one. When processing fails or no dog is detected, the credit-handling logic prevents the user from paying for a result that was not delivered.

The ten-judge system:
The judges form a recurring cast. Each judge has a recognizable attitude and a different comedic framework: 
The Special Critic:
Cold. Tactical. Disappointed. An arrogant football manager who treats ordinary dog behavior as tactical indiscipline, dressing-room betrayal, or a failure to respect the system.
Kitchen Tyrant:
Screaming culinary outrage. A furious head chef who interprets posture, staring, begging, sleeping, mud, and household destruction as catastrophic failures of dinner service.
HR Investigator:
Formal complaints. Zero empathy. A professionally exhausted HR officer who turns harmless dog behavior into a workplace incident, documents it, and imposes an unnecessary corporate consequence.
Court of Snacks:
Dramatic legal verdicts. The dog's behavior becomes evidence in an absurd snack-related trial, complete with charges, rulings, and punishment.
Chaos Queen:
Glorious nonsense. Maximum damage. She treats disorder as performance, encourages escalation, and celebrates the dog's ability to make a normal situation unnecessarily dramatic.
Life Coach:
Judges your dog. Judges you. The dog becomes proof that both the pet and the owner require an urgent and deeply questionable personal transformation.
The Ex-Wife:
Takes half your treats. Every photo becomes evidence in a personal dispute involving resentment, suspicion, emotional accounting, and snack custody.
HOA President:
Fining you for existing. The dog is treated as a neighborhood violation involving lawns, windows, noise, unauthorized gatherings, property lines, or rules that were apparently written five minutes ago.
Vet Tech:
Judging your choice of kibble. The dog is examined through the voice of someone who has seen everything, remembers every questionable owner decision, and has no patience left for excuses.
Cat Lover:
Disgusted by the barking. The dog is evaluated by someone who considers feline behavior the natural standard and views every canine habit as further evidence for the prosecution.

The purpose of the cast is consistency. A user should begin recognizing how each judge thinks, not merely receive a different random tone every time.

Technology used:
Bad Poochy was built with:
React Native and TypeScript for the application
Expo for the development workflow and native builds
React Navigation for the screen flow
Supabase Authentication for anonymous, email, Google, and Apple-linked accounts
Supabase Postgres for user profiles, credit balances, and judge access
Supabase Edge Functions for authenticated server-side processing
OpenAI's multimodal API for visual analysis and structured comedic generation
RevenueCat for iOS and Android in-app purchases, entitlements, and purchase restoration
Expo Image Picker and Image Manipulator for photo selection and preparation
React Native View Shot for rendering shareable verdict cards
Expo Media Library and Expo Sharing for saving and sharing results
EAS Build, App Store Connect, and Google Play Console for distribution
The mobile app never contains the Supabase service-role key. Administrative operations, including account deletion, remain inside server-side Edge Functions. The current implementation also does not upload user photos into a Supabase Storage bucket.

What Apple's review process forced me to improve: 
Apple's review process was frustrating at times, but it materially improved the product. Each review exposed a real gap between having a feature in the code and delivering a complete user experience. 

Reliable in-app purchases
An early iPad review could not load the purchase packs. Resolving it required checking the Paid Apps Agreement, App Store product availability, RevenueCat product mapping, and the complete purchase path inside a native TestFlight build.

Functional controls
Apple found a gear icon that did not perform an action. That forced me to stop treating visible interface elements as decoration. The gear now opens the account screen, where users can manage how they continue with the app and access account deletion.

Complete product submissions
The app binary originally reached review without all three in-app purchase products being attached to the same submission. I learned that creating the products in App Store Connect was not enough. The binary, Starter Pack, Full Chaos Pack, and Bulk Refill needed to be submitted together with complete review metadata and screenshots. 

Restore Purchases
Apple required a distinct, user-initiated Restore Purchases button for restorable products. I added it directly below the purchase cards and connected it to RevenueCat's restoration flow. The app restores judge access from the Starter Pack and Full Chaos Pack while avoiding the regranting of consumable refill credits.

Account deletion
Apple also required users who could create an account to be able to delete it inside the app. I restored the gear navigation, added a visible Delete Account control and confirmation step, and built a Supabase Edge Function that deletes the corresponding user_profiles record and Supabase Auth user from the server. For accounts connected through Sign in with Apple, the deletion flow also refreshes and revokes the Apple authorization before deleting the account. After deletion, the app clears the local session and RevenueCat user connection, returns to the beginning, and can create a fresh anonymous profile with one free credit.

iPad layout support
Apple's landscape iPad testing revealed compressed and overlapping text in the pricing cards. The same screen looked correct in portrait, which made the problem easy to miss. I revised the tablet-specific spacing, card proportions, text sizing, and responsive layout, then tested both orientations in TestFlight before resubmitting.
After these changes, Bad Poochy was approved and released on the App Store.

How I designed prompts around visible photo details:
The hardest part was not connecting an image model to an app. The hard part was preventing ten characters from collapsing into the same generic joke. Early versions could overlook the most interesting part of a photo, invent an object that was not present, switch topics halfway through a roast, or repeatedly choose the same safe ending. 
I gradually rebuilt the prompts around a stricter approach: 
Begin with visible evidence: Each roast starts with something actually visible in the photo, such as an expression, pose, object, action, or spatial relationship.
Do not invent supporting details: The judges can exaggerate what they see for comic effect, but the details they build on must come from the photo.
Translate the evidence through one character: Each judge interprets the same photo through a different personality. HR might see a workplace incident, while the HOA President might see a neighborhood violation.
Keep one comic argument: The opening, escalation, and ending should build on the same idea so the roast feels like one joke rather than several unrelated observations.
Use photo-specific approaches without eliminating improvisation: Certain visible situations have tailored comic approaches for each judge. The model can improvise when it finds something sharper, but the joke still has to match the photo and the judge’s personality.
Write for the card, not an essay: Each judge has length and formatting rules designed around the space available in the final verdict card.
Reject generic punchlines: A line about chaos, bad behavior, or a policy violation needs to go further than simply naming the problem. The ending should deliver a specific consequence that fits the judge.
Return structured output: Each response follows a judge-specific JSON format so the front end receives predictable fields instead of an uncontrolled block of text.

The prompts continue to evolve because comedy becomes repetitive very quickly. A line can be funny once and become mechanical after appearing five times. Much of the ongoing work is therefore not adding more words, but removing generic instructions, reducing overused examples, and making the model look harder at the photograph before it decides what the joke is.

Try Bad Poochy

iOS: https://apps.apple.com/us/app/bad-poochy/id6767693706

Android: https://play.google.com/store/apps/details?id=com.badpoochy

Bad Poochy was created by Jun Kuang through JM16 Studios.
