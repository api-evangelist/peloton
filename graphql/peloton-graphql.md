# Peloton GraphQL Schema

## Overview

This is a conceptual GraphQL schema for the Peloton connected fitness platform. Peloton does not publish a public developer API or official GraphQL endpoint. This schema is derived from community reverse-engineering efforts (notably the [pylotoncycle](https://github.com/cyclographs/pylotoncycle) SDK and related projects) and reflects the data model exposed through Peloton's internal REST API at `api.onepeloton.com`.

The schema covers the full breadth of Peloton's platform: authentication, user profiles, subscriptions, equipment, instructors, fitness classes, workouts and performance metrics, leaderboards, social features, challenges, scheduling, notifications, and media assets.

## Schema Source

- Community SDK: https://github.com/cyclographs/pylotoncycle
- Unofficial API base URL: https://api.onepeloton.com
- Schema type: Conceptual / Reverse-engineered

## Top-Level Types

### Identity and Authentication

- `AuthToken` — session token returned after login
- `DeviceLogin` — login from a Peloton hardware device
- `PlatformToken` — platform-specific OAuth token
- `Session` — active user session

### Users and Profiles

- `User` — core account record
- `UserProfile` — extended profile (avatar, location, bio)
- `FollowerStats` — aggregate follower/following counts
- `FollowSummary` — follow relationship between two users
- `FriendRequest` — pending follow/friend request

### Subscriptions and Memberships

- `Subscription` — digital or all-access membership record
- `Membership` — hardware-linked membership tier
- `AppSubscription` — app-only subscription (no hardware)

### Hardware and Devices

- `Device` — registered Peloton device
- `Bike` — Peloton Bike or Bike+
- `Treadmill` — Peloton Tread or Tread+
- `Row` — Peloton Row
- `Guide` — Peloton Guide (camera-based strength training device)

### Instructors

- `Instructor` — instructor record with name, specialties, image
- `InstructorBio` — extended instructor biography and background

### Classes

- `Class` — base class record (shared fields)
- `ClassType` — category such as cycling, running, yoga, strength
- `LiveClass` — a scheduled real-time broadcast class
- `OnDemandClass` — recorded class available on demand
- `ScheduledClass` — a class a user has saved to their calendar
- `Bookmark` — a class saved to a user's personal list
- `Program` — a multi-week structured fitness program
- `ProgramTag` — tag applied to a program (e.g., beginner, weight loss)

### Workouts and Performance

- `Workout` — a completed workout session
- `WorkoutSummary` — high-level stats for a completed workout
- `WorkoutMetrics` — time-series performance data for a workout
- `HeartRate` — heart rate samples during a workout
- `Output` — power output (watts) samples
- `Cadence` — pedaling cadence samples
- `Resistance` — resistance level samples
- `Pace` — pace samples (for tread/row workouts)

### Leaderboards and Achievements

- `Leaderboard` — leaderboard for a specific class or challenge
- `LeaderboardEntry` — a single user's position on a leaderboard
- `Streak` — consecutive day or week workout streak
- `Achievement` — an unlocked achievement or milestone
- `Badge` — a badge awarded for reaching a goal
- `PBRecord` — personal best record for a class type or metric

### Challenges

- `Challenge` — a community or brand-sponsored fitness challenge
- `ChallengeAchievement` — an achievement unlocked within a challenge

### Content and Media

- `Genre` — music genre tag for a class
- `MusicPlaylist` — playlist used in a class
- `MediaAsset` — image, video, or audio asset
- `Transcript` — full text transcript of a class
- `Caption` — timestamped caption line within a transcript

### Tags and Discovery

- `Tag` — user-defined or system tag for organizing content

### Notifications and Scheduling

- `Notification` — in-app notification record
- `Schedule` — instructor or class release schedule

## Query Examples

```graphql
# Fetch the authenticated user's profile
query Me {
  me {
    id
    username
    profile {
      displayName
      location
      avatarUrl
    }
    subscription {
      status
      tier
      expiresAt
    }
  }
}

# Fetch recent workouts
query RecentWorkouts($userId: ID!, $limit: Int) {
  workouts(userId: $userId, limit: $limit) {
    id
    startedAt
    duration
    classType {
      name
    }
    summary {
      totalOutput
      avgCadence
      avgHeartRate
      calories
    }
  }
}

# Fetch a leaderboard for a class
query ClassLeaderboard($classId: ID!) {
  leaderboard(classId: $classId) {
    entries {
      rank
      user {
        username
      }
      totalOutput
    }
  }
}
```

## Notes

- Peloton's production API is REST-based, not GraphQL. This schema represents an idiomatic GraphQL translation of the same data model.
- Authentication uses session cookies (`peloton_session_id`) set at login; all subsequent requests require this cookie.
- Rate limits are enforced but not publicly documented.
- The unofficial API and community SDKs may break without notice when Peloton updates its internal API.
