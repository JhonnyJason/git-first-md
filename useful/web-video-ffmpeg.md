# Web Video Optimization and ffmpeg
First of all a shoutout to [ffmpeg](https://ffmpeg.org/about.html).
They are amazing, and especially if you are a developer - we highly recommend  you to check out [what they created](https://ffmpeg.org/documentation.html).

> thank you image goes here

We want to offer self-hosted videos to our beloved users surfing the web. However we donot want to just upload 1GB of video to our webserver and serve it "as is" as this would be **insane** for the visitors using limited mobile data as well for our precious webspace. We want to optimze:

- Speed, download and play
- Size, as small as possible
- Quality, well acceptable quality
- Compatibility, play on most every device
- Streamability, support data Range Download

ffmpeg the [cli tool](https://ffmpeg.org/ffmpeg.html) has us covered for most parts. It is what we use to do all the magic for us.

The magic command we use is:
```bash
ffmpeg -i input-video.mp4 -movflags +faststart -c:a copy -c:v libx264 -crf 24 -preset veryslow -vf scale="1280:-2:flags=lanczos" -pix_fmt yuv420p -profile:v baseline -level 3.2 -tune film output-video.mp4
```

## Our Chosen Flags

- `-movflags +faststart` makes sure that the metadata are written first before the movie data are. This makes it streamable and faster to start.
- `-c:a copy` ensures that the audiotrack is not reencoded. Here we assume that our video and audio is already in a lossy format, thus we introduce worse quality with no significant reduction in size. If your really want to change audio encoding, be sure to start off your highest quality audio and use `-c:a libopus`
- `-c:v libx264 -crf 24 -preset veryslow` the video encoding set to H.264 with a relatively high crf und most computation time possible. The higher the crf value the more the video is being compressed and loses more quality - in our opinion 23 or 24 works best. Also we squeeze more quality out of a lesser filesize with `-preset veryslow` as we don't optimize for encoding time. Because of compatibility we don't use [AV1](https://caniuse.com/av1) nor [H.265](https://caniuse.com/hevc). With [H.264](https://caniuse.com/mpeg4) we have all relevant platforms covered, and even though the reduction in size to AV1 and H.265 is significant, it is not worth it to use 2 smaller videos instead of one larger video (maybe if the reduction in size is over 50% for each). In the future it seems AV1 could be the better choice for compatibility reasons.
- `-vf scale="1280:-2:flags=lanczos"` reduces the video size to a specific width of 1280. For web videos this is our default for anything larger than that. In our opinion this is enough if we optimize for speed and size. The scaling flag `lanczos` should improve quality of the scaling step.
- `-pix_fmt yuv420p` adds compatibility (e.g QuickTime)
- `-profile:v baseline -level 3.2` sets compression complexity to most simple for faster decoding and greater compatibility - level 3.2 means we could do 1280 × 1024 @42.2fps which fits perfect when we reduce the size of the videos to 1280xYYY and would have fps below 30.
- `-tune film` tells ffmpeg to tune for high quality video. As we already limited the profile complexity to baseline For presentation slides `-tune stillimage` might be better suited - yet it seems tune to film yiels better compression.

## More Options

- `-filter:v fps=30` depending on the input video it makes sense to reduce the framerate. However we noticed most videos have lesser frame-rate so it is not in our magic command.
