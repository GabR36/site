{{ $author := index .Site.Author (.File.ContentBaseName | lower) }}
+++
title = '{{ $author.Name | title }}'
date = {{ .Date }}
layout = 'default'
+++