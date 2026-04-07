# Repository Notes

## Git
- Do not run git commands that can heavily load the HDD in parallel.
- Prefer sequential git operations, especially for `status`, `diff`, `add`, `commit`, `push`, and branch switching.

## OCR Dictionary Context
- For UI/OCR runs, the active dictionary folder is `Se.DictionariesFolder`, not the repo-root `Dictionaries` folder.
- In portable/local debug runs, `Se.DictionariesFolder` resolves under `AppContext.BaseDirectory`.
- In this workspace's local debug build, the runtime dictionaries were observed at:
  `src/UI/bin/Debug/net10.0/Dictionaries`
- Before concluding that a word "did not save", inspect the runtime `*_user.xml` in `Se.DictionariesFolder`.

## OCR Unknown Word Flow
- Main OCR unknown-word UI logic lives in:
  `src/UI/Features/Ocr/OcrViewModel.cs`
- OCR spell-check/runtime dictionary state lives in:
  `src/UI/Features/Ocr/FixEngine/OcrFixEngine2.cs`
- User word and name list loading/runtime caches live in:
  `src/UI/Features/SpellCheck/SpellCheckWordLists2.cs`
- Direct XML helpers live in:
  `src/UI/Logic/Dictionaries/UserWordsHelper.cs`
  `src/libse/Common/Utilities.cs`

## Confirmed OCR Bugs / Fix History
- Fix 1 branch:
  `codex/fix-ocr-user-dictionary-fixedword`
- Fix 1 problem:
  after `Add to user dictionary`, OCR could still ask the same word again in the same session because the runtime OCR dictionary state was not refreshed.
- Fix 1 implementation:
  added `AddUserWord(string word)` to `IOcrFixEngine2`/`OcrFixEngine2` and updated OCR session state immediately.

- Fix 2 branch:
  `codex/fix-ocr-user-dictionary-displayed-word`
- Fix 2 problem:
  some OCR add-actions used the raw OCR token (`Word.Word`) instead of the displayed/corrected word (`FixedWord` or the edited popup text).
- For this bug family, check:
  `src/UI/Features/Ocr/UnknownWordItem.cs`
  `src/UI/Features/Ocr/OcrViewModel.cs`

## OCR Example Case
- Example reported case:
  `recuérdalo`
- Confirmed behavior:
  the word was correctly written to `es_ES_user.xml`, so the repeated prompt was caused by stale in-memory OCR state, not by a bad XML write.

## Useful Test Commands
- Targeted OCR fix-engine tests:
  `dotnet test tests\UI\UITests.csproj --filter "FullyQualifiedName~UITests.Features.Ocr.FixEngine" --no-restore`
- Specific regression tests should live under:
  `tests/UI/Features/Ocr`
