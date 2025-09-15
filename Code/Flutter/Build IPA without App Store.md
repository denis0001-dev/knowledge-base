#flutter #ipa #ios #apple #code #bash 

_Original source: <https://marc-ko.github.io/2024/12/10/flutter_ipa>_

```bash
# 1. Build the IPA
flutter build ios --release

# 2. Prepare the archive
cd build/ios
mkdir Payload
mv iphoneos/Runner.app Payload

# 3. Create the final IPA
zip -qq -r -9 Runner.ipa Payload
open .
```