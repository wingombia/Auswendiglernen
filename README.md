# Auswendiglernen

An [AnkiDroid](https://docs.ankidroid.org/) clone. Final project for Android 101 - HCMUS.

# Table of Contents

- [Team members](#team-members)
- [API](#api)
- [Storage](#storage)

# Team members

- 1712092
- 1712760
- 1712932

# API

In the following parts, I will use `X[]` to refer to `ArrayList<X>`, where `X` is some data type.

1. CardTemplate

   1. Properties:
      - `String templateFront`
      - `String templateBack`
      - `String styling`
   2. Methods:
      - Getters.
      - Setters.
      - `CardTemplate()`: default constructor, initialises with values `("{{front}}", "{{back}}", "")`.
      - `CardTemplate(String templateFront, String templateBack, String styling)`.
      - `Card render(String[] fieldList, String[] valueList)`: create a Card instance by substituting all `{{field}}` with the corresponding `value`.

2. NoteType

   1. Properties:
      - `String id`: auto generated using `java.util.UUID`.
      - `String name`
      - `String[] fieldList`
      - `CardTemplate[] templateList`
   2. Methods:
      - Getters.
      - Setters.
      - `NoteType(String name, String[] fieldList, CardTemplate[] templateList)`.
      - `NoteType(NoteType)`: copy constructor.

3. Note

   1. Properties:
      - `NoteType noteType`
      - `String[] valueList`
      - `Card[] cardList`: list of cards generated from this note. This list is re-generated everytime `getCardList()` is called.
   2. Methods:
      - Getters, Setters for `noteType` and `valueList`.
      - `Card[] getCardList()`: return list of cards generated from this note.
      - `Note(NoteType noteType, String[] valueList)`.

4. Card

   1. Properties:
      - `String htmlFront`: rendered `CardTemplate.templateFront` with `Note.valueList`.
      - `String htmlBack`: rendered `CardTemplate.templateBack` with `Note.valueList`.
      - `String css`: rendered `CardTemplate.styling` with `Note.valueList`.
      - `String attr`: placeholder value for additional attributes (`learningState`, `dueTime`, etc).
   2. Methods: None. All properties are public and thus can be accessed via the . operator.

5. Deck

   1. Properties:
      - `String name`: deck's name.
      - `Note[] noteList`: list of notes in this deck.
   2. Methods:
      - Getters, Setters.

# Storage

## Summary

Everything is stored within 2 files: `note-type.json` and `deck.json`, where:

- `note-type.json`: stores data about NoteType and CardTemplate.
- `deck.json`: stores data about Deck, Note and Card.

The app's assets will contain 2 instance of `note-type.json` and `deck.json`. These 2 are the default data for users that have just installed the app.

For users that have used the app (creating NoteType, creating Note, etc, basically modifying the default data), the local data for the app on users' machines will contain 2 instance of `note-type.json` and `deck.json`. These 2 are the current user's data and can be exported to other users.

## Structure

Structure of the 2 files are as follows:

1. `note-type.json`

   ```
   [
      {
         "id": "1",
         "name": "Basic",
         "fieldList": [
            "front",
            "back",
            "page"
         ],
         "templateList": [
            {
               "templateFront": "Front: {{front}}. Page: {{page}}",
               "templateBack": "Back: {{back}}",
               "styling": ".card { color: red; };"
            },
            {
               "templateFront": "{{front}}",
               "templateBack": "{{back}}",
               "styling": ".card { color: blue; };"
            }
         ]
      },
      {
         "id": "2",
         "name": "Basic 2",
         "fieldList": [
            "front",
            "back"
         ],
         "templateList": [
            {
               "templateFront": "Front: {{front}}",
               "templateBack": "Back: {{back}}",
               "styling": ".card { color: red; };"
            }
         ]
      }
   ]
   ```

2. `deck.json`
   ```
   [
      {
         "name": "Deck #1",
         "noteList": [
            {
               "noteTypeId": "1",
               "valueList": [
                  "front value",
                  "back value",
                  "page number"
               ],
               "cardList": [
                  {
                     "attr": "attr 1"
                  },
                  {
                     "attr": "attr 2"
                  }
               ]
            },
            {
               "noteTypeId": "2",
               "valueList": [
                  "front value",
                  "back value"
               ],
               "cardList": [
                  {
                     "attr": "attr 1"
                  }
               ]
            }
         ]
      },
      {
         "name": "Deck #2",
         "noteList": [
            {
               "noteTypeId": "1",
               "valueList": [
                  "front value 123",
                  "back value 123",
                  "page number 123"
               ],
               "cardList": [
                  {
                     "attr": "attr 1"
                  },
                  {
                     "attr": "attr 2"
                  }
               ]
            },
            {
               "noteTypeId": "2",
               "valueList": [
                  "front value 123",
                  "back value 123"
               ],
               "cardList": [
                  {
                     "attr": "attr 1"
                  }
               ]
            }
         ]
      }
   ]
   ```

## Notes

In `deck.json`, Cards are represented only by `attr` (which is a placeholder for actual value such as `learningState` or `dueTime`). The other attributes (`htmlFront`, `htmlBack`, `css`) are generated everytime they are called.

## Methods

1. DataReader

   - `static Pair<ArrayList<NoteType>, ArrayList<Deck>> initApp(Context context)`: load user's data in fashion defined in [Summary](#summary)

   - `static ArrayList<NoteType> loadNoteTypeFromFile(File f)`: load NoteType and CardTemplate data from File.

   - `static ArrayList<Deck> loadDeckFromFile(File f, ArrayList<NoteType> typeList)`: load Deck data from File. This method requires a `NoteType[]` for reference to parse NoteType from `noteTypeId`.

2) DataWriter

   - `static void writeType(ArrayList<NoteType> typeList, Context context)`: write NoteType and CardTemplate data to user's local storage.

   - `static void writeDeck(ArrayList<Deck> deckList, Context context)`: write Deck, Note and Card data to user's local storage.