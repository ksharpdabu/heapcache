# heapcache
[![Build Status](https://travis-ci.org/turboezh/heapcache.svg)](https://travis-ci.org/turboezh/heapcache)
[![GitHub release](https://img.shields.io/github/release/turboezh/heapcache.svg)](https://github.com/turboezh/heapcache/releases)
[![Go Report Card](https://goreportcard.com/badge/github.com/turboezh/heapcache)](https://goreportcard.com/report/github.com/turboezh/heapcache)
[![Maintainability](https://api.codeclimate.com/v1/badges/de484103003b548529f0/maintainability)](https://codeclimate.com/github/turboezh/heapcache/maintainability)
[![Test Coverage](https://api.codeclimate.com/v1/badges/de484103003b548529f0/test_coverage)](https://codeclimate.com/github/turboezh/heapcache/test_coverage)
![Downloads](https://img.shields.io/github/downloads/turboezh/heapcahce/total.svg)
[![GoDoc](https://godoc.org/github.com/turboezh/heapcache?status.svg)](https://godoc.org/github.com/turboezh/heapcache)

This cache implementation is based on priority queue (see [Heap](https://golang.org/pkg/container/heap/)).

! Prior v1.0.0 API may be broken at any time. 

Features:
 - simple standard data structure;
 - interface based;
 - no write locks on get operations;
 - capacity may be changed at any time.

# Documentation
https://godoc.org/github.com/turboezh/heapcache


# Examples

## Cache item

Define your own type that implements heapcache.Item interface.
```go
type CacheItem struct {
	Key      string
	Value    string
	Priority int
}
// CacheKey may return any key type (see https://golang.org/ref/spec#KeyType)
func (i *CacheItem) CacheKey() heapcache.KeyType {
	return i.Key
}
// Item
func (i *CacheItem) Less(other Item) bool {
	return i.Priority < other.(*CacheItem).Priority
}
```
or
```go
// or
type String string

func (s String) CacheKey() heapcache.KeyType {
	return s
}

func (s String) Less(other Item) bool {
	return len(s) < len(other.(String))
}
```

## Add item
```go
cache := heapcache.New(3)

// add one item
cache.Add(&CacheItem{"foo", "bar", 1})

// add many items at once
cache.AddMany(
	&CacheItem{"foo", "bar", 1},
	&CacheItem{"go", "lang", 100500},
)
```

## Get item
```go
item, exists := cache.Get("foo")
if !exists {
    // `foo` doesn't exists in cache
    // `item` is nil
}
// cache returns `heapcache.Item` so we need to assert type (if need so)
item = item.(*CacheItem)
```

## Check item
```go
// check if cache contain all keys 
ok := cache.Contains("foo", "go")

// check if cache contain any of keys 
ok := cache.Any("foo", "go")

// Remove returns false if there is no item in cache
wasRemoved := cache.Remove("foo3")
```

## Remove item
```go
// Remove returns false if there is no item in cache
wasRemoved := cache.Remove("foo3")
```
