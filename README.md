# Architectural Decision

An [Architectural Decision](https://en.wikipedia.org/wiki/Architectural_decision) is a design decision that could potentially have a large impact on your codebase. Why those decisions were made, from a technical perspective and a business perspective, are important, so they should be documented properly. This library allows you to document Architectural Decision Records (ADR) as an Attribute within your codebase. Doing so provides some functionality that might be useful:

- Architectural Decisions are close to your codebase. As in, the decision is right there in the repo, and you don't have to hunt through another system to find it.
- Architectural Decisions are _code_ in your codebase. As an Attribute you can mark the places in your code that are impacted by this decision. This makes it easier to remember for existing maintainers and easier for new developers to realize there's relevant information. If you implement the ADR following the conventions of this library PHPStorm and other IDEs will show you that decision simply by hovering over the Attribute.
- Statically analyze the impact of your decision. Over time as more of your codebase becomes annotated with Attributes you may be able to glean more information about the decision.

> This software is still in early-development!

## Installation

```shell
composer require --dev cspray/architectural-decision:dev-main
```

## Requirements

Before using this library there are a couple assumptions that I make, especially when using the provided `bin/architectural-decisions` CLI tool. If you're using Composer for installing and autoloading the assumptions are pretty safe, but they're worth pointing out.

1. There's a `composer.json` file present in the root of your project.
2. That configuration has an `autoload` with a `psr-4` or `psr-0` entry that specifies 1 or more directories in the root of your project.
3. Inside the directories specified in your autoloader at least 1 Attribute implementing `ArchitecturalDecisionRecord` exists.
4. All `ArchitecturalDecisionRecord` implementations should be able to be created with no constructor dependencies.

If these assumptions don't hold up for you the bulk of the functionality provided by this library is in the `Cspray\ArchitecturalDecision\ArchitecturalDecisionAttributeGatherer` and the `Cspray\ArchitecturalDecision\XmlDocumentGenerator` implementations. It would be fairly straightforward to implement this library with your own assumptions.

## Usage Guide

The first thing to do is implement an Architectural Decision Record! This is handled with the `Cspray\ArchitecturalDecision\ArchitecturalDecisionRecord` interface. I recommend you use the abstract `Cspray\ArchitecturalDecision\DocBlockArchitecturalDecision` class. This implementation will use the DocBlock for the Attribute as the contents of the decision.

```php
<?php declare(strict_types=1);

// src/ArchitecturalDecisions/MyFirstDecision.php

namespace Acme\ArchitecturalDecisions;

use Cspray\ArchitecturalDecision\DocBlockArchitecturalDecision;
use Attribute;

/**
 * Explain the decision and its potential business impact. 
 */
#[Attribute]
class MyFirstDecision extends DocBlockArchitecturalDecision {

    public function getTitle() : string {
        return 'My First Decision';
    }
    
    public function getDate() : string {
        return '2022-07-19';
    }

}
```

Optionally, you can also annotate appropriate places in your codebase where it might make sense to do so. After that you can run a command to generate an XML document detailing all of your decisions and where they were annotated in your codebase.

```shell
./vendor/bin/architectural-decisions
```

If successful a file named `architectural-decisions.xml` will be generated and stored in the root of your project. You can use this file to generate views into the content, assist in static analysis, or whatever else you might want to do with the ADR information and where it is used in your codebase.
