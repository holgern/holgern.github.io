---
layout: default
title: Home
description: Practical notes on NixOS, Neovim, tmux, and reproducible Python workflows.
---

# nixwerk

Practical notes on NixOS, Neovim, tmux, and reproducible Python workflows.

I write about developer setups, reproducible environments, terminal workflows, and occasional experiments with audio and TTS.

## Recent posts

{% for post in site.posts limit:5 %}
- [{{ post.title }}]({{ post.url | relative_url }}) — {{ post.date | date: "%Y-%m-%d" }}
{% endfor %}

## Topics

- NixOS
- Neovim
- tmux
- Python
- Audio / TTS
