# Owncast Webhook Data Documentation

This project documents the data structures Owncast sends to third-party applications through its webhook system.

## Purpose

Owncast is an open-source, self-hosted live streaming server. It can notify external applications about events — like chat messages or viewer activity — through webhooks. This documentation describes:

- the data objects included in webhook payloads (User, Chat Message)
- key viewer metrics exposed through webhooks
- terminology used across Owncast's webhook system

## Contents

**Data objects:** User, Chat Message

**Metrics:** Current Viewer Count, Session Max Viewer Count, Overall Max Viewer Count

**Glossary:** key terms used in Owncast's webhook system

## Source

Based on Owncast's public webhook documentation: https://owncast.online/thirdparty/webhooks/