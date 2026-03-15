# GitHub-Repository-Analysis
A configurable C# dot net CLI tool that integrates with GitHub APIs to evaluate pull request interactions and developer review metrics across organizational repositories.

##  Summary of relationships
•	Program —> PullRequestsStatisticsDisplay : dependency / association (creates and calls DisplayPullRequestsStatistics()).

•	PullRequestsStatisticsDisplay —> PullRequests : dependency / association (instantiates/uses to get/display PRs).

•	PullRequestsStatisticsDisplay —> EngagementClass, EvolutionClass, DesignReviewStandard, CodingReviewStandard, PullRequestFiles, CodeReviewClosureTime, PullRequestsDoneInLastNDays, WordDocumentGenerator : dependency / association (creates local instances and calls their methods; no long‑lived ownership).

•	EngagementClass —> PullRequests : dependency (calls GetLastNPullRequests(string, string, int, string) and then queries PR comments).
•	EvolutionClass —> PullRequests : dependency (calls GetLastNPullRequests(string, string, int, string) and then queries PR commits).

•	DesignReviewStandard —> PullRequests : dependency (calls GetLastNPullRequests(string, string, int, string) and inspects PR comments).
•	CodingReviewStandard —> PullRequests : dependency (same pattern).

•	PullRequestFiles —> PullRequests : dependency (calls GetLastNPullRequests(string, string, int, string) to iterate PRs).
•	PullRequestFiles contains nested PullRequestFile DTO used only internally: composition (part-of, private helper used for deserialization).

•	CodeReviewClosureTime —> PullRequests : dependency (uses GetLastNPullRequests(string, string, int, string) to compute averages).
•	CodeReviewClosureTime also has an internal helper IsPullRequestOpen (local function) — internal implementation detail (composition-like).

•	PullRequestsDoneInLastNDays : queries GitHub API directly (not always using PullRequests), but conceptually depends on PR data — dependency. It has a nested PullRequest DTO for deserialization: composition.

•	PullRequests —> PullRequestClass : association / aggregation (creates and returns PullRequestClass instances as DTOs; PullRequestClass instances are independent data objects returned to callers).
•	PullRequests (static overload) —> Octokit library : dependency (uses Octokit.GitHubClient to read PR files).

## Notes on type choices:
•	Most relationships are simple dependencies/associations (caller creates/uses another class locally); there is no long‑lived ownership field suggesting aggregation, nor strict lifecycle containment suggesting composition, except for private nested DTO classes (composition) which exist only as implementation parts.
•	No interfaces or inheritance relationships are present; no realizations.
•	Several classes also have implicit dependency on external resources (GitHub REST API / HttpClient) — treat those as external service dependencies.
