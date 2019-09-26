# Valkyrie 2 Demo
This is a brief command-line demo to show how the [Valkyrie](https://github.com/samvera/valkyrie) API works.

## pre-requisites
* ruby 2.6
* bundler (`gem install bundler`)

## download this repository and install dependencies
```sh
$ git clone https://github.com/escowles/v2demo
$ cd v2demo
$ bundle install
```

## start an interactive ruby session
```
$ irb
> require "valkyrie"
```

## setup our persistence
```
metadata = Valkyrie::Persistence::Memory::MetadataAdapter.new
files = Valkyrie::Storage::Memory.new
```

## define some models
```
class Page < Valkyrie::Resource
  attribute :title
  attribute :file_identifiers, Valkyrie::Types::Set
end

class Book < Valkyrie::Resource
  attribute :title
  attribute :member_ids, Valkyrie::Types::Array.meta(ordered: true)
end
```

## create some objects
```
page1 = Page.new(title: "page 1")
page2 = Page.new(title: "page 2")
saved_page1 = metadata.persister.save(resource: page1)
saved_page2 = metadata.persister.save(resource: page2)

book1 = Book.new(title: "I Like Short Books", member_ids: [saved_page1.id, saved_page2.id])
saved_book1 = metadata.persister.save(resource: book1)
```

## queries
```
metadata.query_service.find_all
metadata.query_service.find_all_of_model(model: Book)
metadata.query_service.find_by(id: saved_page1.id)
```

## deleting objects
```
metadata.persister.delete(resource: saved_book1)
metadata.query_service.find_by(id: saved_book1.id)
```

## working with files
```
tempfile = File.new("v2hex.png")
file = files.upload(file: tempfile, original_filename: "v2hex.png", resource: saved_page1)
saved_page1.file_identifiers = [file.id]
```

## deleting files
```
store.delete(id: file.id)
```
