## General Flutter tips

- [Dart Minimum Execution Time](#min-execution-time)
- [Generic DTO Idea](#generic-dto-idea)


#### Dart Minimum Execution Time <a id="min-execution-time"></a>

Function to serve as a minimum guaranteed delay, with usage example

```dart
import 'dart:async';

Future<T> executeWithMinimumDuration<T>(
  Future<T> Function() function, 
  Duration minimumDuration,
) async {
  Stopwatch stopwatch = Stopwatch()..start();

  // Execute the function and store the result
  T result = await function();

  // Stop the stopwatch
  stopwatch.stop();

  // Calculate the remaining time to wait
  final int remainingTime = minimumDuration.inMilliseconds - stopwatch.elapsedMilliseconds;

  // If the execution time was less than the minimum, wait for the remaining time
  if (remainingTime > 0) {
    await Future.delayed(Duration(milliseconds: remainingTime));
  }

  // Return the result of the function
  return result;
}

// Example usage with a function that has parameters and a return value
Future<String> fetchUserData(String userId) async {
  // Simulate network request
  await Future.delayed(Duration(milliseconds: 100)); // Simulating quick execution
  return "UserData for $userId";
}

void main() async {
  final String result = await executeWithMinimumDuration(
    () => fetchUserData("123"), 
    Duration(milliseconds: 300),
  );

  print(result); // Output will be the fetched user data
}
```

----

#### Generic DTO Idea <a id="generic-dto-idea"></a>

Example to serve as an idea of a generic DTO on a Clean Architeture structure

```dart
class ProfileDto {
  ProfileEntity? profileEntity;
  ProfileCollection? profileCollection;

  ProfileDto({this.profileEntity, this.profileCollection});
  ProfileDto.fromMap(Map<String, dynamic> map) {
    profileEntity = ProfileEntity.fromMap(map);
  }

  ProfileEntity toEntity() {
    return profileEntity != null ? profileEntity! : ProfileEntity.fromMap(profileCollection!.toMap());
  }

  ProfileCollection toCollection() {
    return profileCollection != null ? profileCollection! : ProfileCollection.fromMap(profileEntity!.toMap());
  }

  Map<String, dynamic> toMap() {
    return profileEntity != null ? profileEntity!.toMap() : profileCollection!.toMap();
  }
}
```
