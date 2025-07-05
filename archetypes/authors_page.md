{{ $author := index .Site.Params.Author (.File.ContentBaseName | lower) }}
+++
title = '{{ $author.Name | title }}'
date = {{ .Date }}
layout = 'default'
+++