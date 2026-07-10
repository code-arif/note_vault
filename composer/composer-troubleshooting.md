# Composer Dependency Troubleshooting Guide (Laravel)

> **Purpose**
>
> This documentation explains common Composer dependency problems, why they happen, how to diagnose them, and how to fix them safely.
>
> This guide is especially useful when deploying Laravel projects to a VPS or production server.

---

# Table of Contents

1. Understanding `composer install`
2. Understanding `composer update`
3. Common Problems
4. Dependency Version Conflicts
5. Security Advisory Errors
6. Lock File Problems
7. PHP Version Problems
8. Diagnostic Commands
9. Safe Recovery Steps
10. Production Best Practices

---

# 1. Understanding Composer

Composer manages PHP dependencies.

There are two important files:

```
composer.json
composer.lock
```

### composer.json

Contains dependency requirements.

Example:

```json
{
    "require": {
        "laravel/framework": "^11.31",
        "laravel/socialite": "^5.18"
    }
}
```

This file tells Composer:

> "I need these packages."

---

### composer.lock

Contains the **exact versions** Composer installed.

Example

```
laravel/framework
11.42.0

laravel/socialite
5.23.2
```

Production servers should install packages using **composer.lock**.

---

# 2. composer install vs composer update

## composer install

Reads

```
composer.lock
```

Installs exactly those versions.

Use this on:

- Production
- VPS
- CI/CD

Never changes package versions.

---

## composer update

Reads

```
composer.json
```

Downloads newer versions.

Creates a new

```
composer.lock
```

Use this only on:

- Local Development

---

# 3. Common Problems

---

## Problem 1

```
Your lock file does not contain a compatible set of packages.
```

Example

```
google/auth requires jwt ^6

firebase/php-jwt
v7.0.2 found
```

### Why?

composer.lock contains incompatible versions.

Example

```
google/auth
↓

requires jwt ^6

BUT

lock file contains

jwt v7
```

Impossible dependency graph.

---

### Symptoms

```
composer install

fails immediately
```

---

### Diagnosis

```
composer why firebase/php-jwt --locked
```

```
composer show firebase/php-jwt --locked
```

```
composer show google/auth --locked
```

```
composer show kreait/firebase-php --locked
```

---

### Fix

Local machine

```
composer update -W
```

or

```
composer update google/auth kreait/firebase-php firebase/php-jwt -W
```

If lock file is corrupted

```
rm composer.lock
rm -rf vendor

composer update
```

---

# Problem 2

Security Advisory

Example

```
Root composer.json requires laravel/framework

affected by security advisories
```

---

### Why?

Composer 2.10 introduced security blocking.

Composer refuses to install packages with known vulnerabilities.

---

### Symptoms

```
composer update

fails

before downloading anything.
```

---

### Diagnosis

```
composer audit --locked
```

```
composer config --list | grep policy
```

---

### Fix

Update Laravel to a secure version.

Temporary workaround

```
composer config --global policy.advisories.block false
```

Not recommended for production.

---

# Problem 3

PHP Version Conflict

Example

```
Package requires

PHP 8.3

Current

PHP 8.4
```

---

### Why?

Package has not added support for latest PHP.

Example

```
kreait/firebase-php

supports

8.1

8.2

8.3

NOT

8.4
```

---

### Symptoms

```
composer install

fails
```

---

### Diagnosis

```
php -v
```

```
composer check-platform-reqs
```

---

### Fix

Option 1

Downgrade PHP.

Option 2

Upgrade package.

Option 3

Ignore platform requirements

(Not recommended.)

```
composer install --ignore-platform-reqs
```

---

# Problem 4

Package Version Conflict

Example

```
Package A

requires

jwt ^6

Package B

requires

jwt ^7
```

Composer cannot satisfy both.

---

### Diagnosis

```
composer why package
```

```
composer prohibits package version
```

Example

```
composer prohibits firebase/php-jwt 6.0
```

---

### Fix

Update all conflicting packages.

```
composer update -W
```

---

# Problem 5

No dependencies installed

Example

```
composer why package

No dependencies installed.
```

---

### Why?

vendor directory does not exist.

---

### Fix

Use

```
--locked
```

Example

```
composer why firebase/php-jwt --locked
```

---

# 4. Useful Diagnostic Commands

## PHP Version

```
php -v
```

---

## Composer Version

```
composer -V
```

---

## Validate composer.json

```
composer validate
```

---

## Installed Package

```
composer show package-name
```

Example

```
composer show laravel/framework
```

---

## Locked Package

```
composer show laravel/framework --locked
```

---

## Package Dependency

```
composer why package
```

---

## Locked Dependency

```
composer why package --locked
```

---

## Why Package Cannot Install

```
composer prohibits package version
```

Example

```
composer prohibits firebase/php-jwt 6.0
```

---

## Security Audit

Installed

```
composer audit
```

Lock file

```
composer audit --locked
```

---

## Check Platform Requirements

```
composer check-platform-reqs
```

---

## Dump Autoload

```
composer dump-autoload
```

---

## Clear Cache

```
composer clear-cache
```

---

## Diagnose Composer

```
composer diagnose
```

---

# 5. Safe Recovery Workflow

When Composer breaks

Step 1

```
composer validate
```

---

Step 2

```
php -v
```

---

Step 3

```
composer diagnose
```

---

Step 4

```
composer audit --locked
```

---

Step 5

```
composer why package --locked
```

---

Step 6

```
composer show package --locked
```

---

Step 7

If dependency conflict

```
composer update -W
```

---

Step 8

If lock file is corrupted

```
rm composer.lock
rm -rf vendor

composer update
```

(Local machine only.)

---

# 6. Production Deployment Best Practices

Never run

```
composer update
```

on production.

Correct workflow

Developer

```
composer update
```

Commit

```
composer.lock
```

Push Git

Server

```
git pull
```

```
composer install
```

Never regenerate dependencies directly on production unless absolutely necessary.

---

# 7. Golden Rules

✅ Use `composer install` on production.

✅ Use `composer update` only on local.

✅ Commit `composer.lock`.

✅ Always keep PHP version compatible.

✅ Use `composer audit` regularly.

✅ Use `composer validate` before deployment.

✅ Run `composer diagnose` if Composer behaves unexpectedly.

---

# 8. Quick Reference Commands

```bash
# PHP Version
php -v

# Composer Version
composer -V

# Validate composer.json
composer validate

# Diagnose Composer
composer diagnose

# Security Audit
composer audit --locked

# Dependency Tree
composer why package --locked

# Conflict Check
composer prohibits package version --locked

# Package Info
composer show package --locked

# Check PHP compatibility
composer check-platform-reqs

# Clear Composer Cache
composer clear-cache

# Dump Autoload
composer dump-autoload

# Update Everything
composer update -W

# Install Dependencies
composer install
```

---

# 9. Real Example (JWT Conflict)

### Error

```
google/auth requires firebase/php-jwt ^6.0

composer.lock contains

firebase/php-jwt v7.0.2
```

### Diagnosis

```
composer why firebase/php-jwt --locked
```

Output

```
google/auth -> ^6.0

kreait/firebase-php -> ^6.3.2

laravel/socialite -> ^6.4 || ^7
```

### Cause

The lock file contained `firebase/php-jwt v7.0.2`, but `google/auth` and `kreait/firebase-php` only accepted version 6.x, creating an impossible dependency graph.

### Solution

Regenerate the lock file with:

```bash
composer update -W
```

or, if the lock file is corrupted (on a local development machine only):

```bash
rm composer.lock
rm -rf vendor
composer update
```

---

# Conclusion

Composer errors are generally caused by one of four categories:

1. Dependency version conflicts
2. Lock file inconsistencies
3. PHP version incompatibilities
4. Security advisory restrictions

The safest troubleshooting approach is:

1. Validate configuration (`composer validate`)
2. Check environment (`php -v`, `composer diagnose`)
3. Inspect dependencies (`composer why`, `composer show`)
4. Resolve conflicts with `composer update -W` on the development machine
5. Deploy using `composer install` with the committed `composer.lock`