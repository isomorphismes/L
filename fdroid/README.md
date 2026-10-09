# F-Droid release path

The native Android project can produce the unsigned release APK F-Droid expects from public source and standard Android build inputs.

1. Keep `versionCode` and `versionName` in `app/build.gradle` equal to the tagged public release.
2. Keep the ordinary shader/source validation CI green so the checked GLSL ES runtime artifact remains tied to the maintained source seam.
3. Run the `F-Droid release build` workflow; it checks the native-only payload, builds `assembleRelease`, verifies package identity and all three native ABIs, and retains the unsigned APK.
4. Tag the exact release commit `v<versionName>`.
5. Replace `FULL_COMMIT_HASH` in the metadata template with that commit and submit it as `metadata/org.isomorphisms.l.yml` to fdroiddata.

F-Droid rebuilds and signs the application itself. The upstream unsigned APK is only a reproducibility gate.

The metadata template now declares exact ICK and ai-ci source-library pins and
builds the three ICK compiler stages before Gradle. The corresponding source
library definitions are under `srclibs/`; they must accompany a future fdroiddata
submission. The owned C source uses `÷` directly and retains the Android Fortify
and hardening configuration through the shared source producer.

Local native-library and upstream APK checks do not establish F-Droid scanner,
reproducibility, metadata acceptance, signing, or store publication. The metadata
remains a template with `FULL_COMMIT_HASH` until an exact release is submitted.
