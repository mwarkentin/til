# Customizing images in Github markdown files for dark mode

Today I came across [this tweet](https://twitter.com/diegohaz/status/1516516547342352391) which suggested that you could specify separate images for light/dark mode in markdown files on Github.

I went ahead and tried it out on Sentry's repo and it [worked great on Github](https://github.com/getsentry/sentry/pull/33799).

## Before

<img width="1145" alt="image" src="https://user-images.githubusercontent.com/67560/164304785-a7a8e2ac-89f6-4742-b498-abc549cf396e.png">

## After

<img width="1061" alt="image" src="https://user-images.githubusercontent.com/67560/164304817-6465a7c2-3fe7-47ff-8d29-2f3c1c0be999.png">

However, if you publish these markdown files anywhere else (like [pypi](https://pypi.org/project/sentry/)) the image will most likely show up twice.

I've opened an [issue for pypi](https://github.com/pypa/readme_renderer/issues/239) to see if this can be resolved there.
