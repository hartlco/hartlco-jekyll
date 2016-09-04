---
author: Martin Hartl
format: post
layout: post
date: 2016-09-04 08:01
title: Minimal iOS MVVM and RxSwift
---

There are a lot of great examples out there on how to implement the [MVVM](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93viewmodel "MVVM Pattern - Wikipedia") pattern for iOS Applications. In a lot of those examples the idea behind MVVM is deeply connected to Reactive Programming. They often become more of an RxSwift or ReactiveCocoa showcase than an easy to follow MVVM example even though MVVM can be perfectly used without any Reactive Programming frameworks.

In the end, the ViewModel needs to signal the View to update its state. This is a perfect use case for RxSwift/Reactive Cocoa but can also be done using the delegate pattern or even KVO. In this post I’m showing an incredible lightweight RxSwift connection with a ViewModel used in a TableViewController. Maybe it can give an easy to understand starting point for using Reactive Programming and MVVM in iOS development.

## Bring the code
Let’s first define the data that it used by the ViewModel to signal a content-change to the TableViewController:

	enum UpdateState {
	    case initial
	    case insert(indexPath: IndexPath)
	    case update(indexPath: IndexPath)
	    case delete(indexPath: IndexPath)
	}

The ViewModel knows about the actual models, when they are inserted, updated or got deleted. It uses the `Change` enum to communicate the type of change that happened at a specific `IndexPath`. A simplified implementation of a ViewModel can look like this:

	class ViewControllerViewModel {
	    
	    private var data: [...] = []
	    private let modelChangeSubject = BehaviorSubject<UpdateState>(value: .initial)
	    
	    var modelChange: Observable<UpdateState> { return modelChangeSubject.asObservable() }
	    
	    func insert(...) {
	        //Save new model and determine indexPath
	        ...
	        modelChangeSubject.onNext(.insert(indexPath: indexPath))
	    }
	    
	    func update(...) {
	        //Update model
	        ...
	        modelChangeSubject.onNext(.update(indexPath: indexPath))
	    }
	    
	    func delete(...) {
	        //Delete model
	        ...
	        modelChangeSubject.onNext(.delete(indexPath: IndexPath(forRow: row, inSection: 0)))
	    }
	    
	    func numberOrRows() -> Int {
	        return data.count
	    }
	    
	    func titleForIndexPath(indexPath: IndexPath) -> String? {
	        return data[indexPath.row].description
	    }
	}

The ViewModel exposes an `Observable`.  The TableViewController uses this variable to subscribe to updates from the ViewModel. Privately the `Observable` is created through an `BehaviourSubject`.  `Subjects` are an `Observer` and `Observable` at the same time. The ViewModel only exposes the `Oberserver` representation of the subject so that the TableViewController is not allowed to also send updates itself.

The `Observable` is now used in the TableViewController to update the TableView according to changes in the ViewModel:

	let _ = viewModel.modelChange.subscribe(onNext: { (state) in
	    switch state {
	    case .insert(let indexPath):
	        self.tableView.insertRows(at: [indexPath], with: .automatic)
	        self.tableView.scrollToRow(at: indexPath, at: .top, animated: true)
	    case .update(let indexPath):
	        self.tableView.reloadRows(at: [indexPath], with: .automatic)
	    case .delete(let indexPath):
	        self.tableView.deleteRows(at: [indexPath], with: .automatic)
	    default:
	        return
	    }
	})

The TableViewController will use the ViewModel again in`numberOfRowsInSection` or `cellForRowAt:indexPath` to know what information should be displayed.

This small example demonstrates how RxSwift can be used to connect a ViewModel and an actual ViewController. I highly suggest to read up the RxSwift documentation to get and understanding on how powerful Reactive Programming is and what to consider about memory management.