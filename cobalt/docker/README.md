# COBALT
> best way to save what you love

## To Run

- [Official cobalt github](https://github.com/imputnet/cobalt/tree/main)
- [Processing Instance Docs github](https://github.com/imputnet/cobalt/blob/main/docs/run-an-instance.md)
- [Front end github](https://github.com/imputnet/cobalt/tree/main/web)

### Docker compose
This now loads both a dockerfile build UI and the local processing instance and posts endpoints to use for processing.
```bash 
# From this directory, 
docker compose up -d 
```

Read the docs for more options + environment variables. Might be interesting to add Watchtower

### UI (just for getting the UI working locally. I now have a dockerfile for building it with the compose)
```bash
# git clone from the source
git clone https://github.com/imputnet/cobalt/tree/main

# navigate to the web directory
cd ./cobalt/web/

# Install vite (if you do not have it )
pnpm add -D vite

# Create the build release
pnpm run build

# Run the build
pnpm run preview
```

## TODO
- [x] Bundle the web into its own docker image / dockerfile 
