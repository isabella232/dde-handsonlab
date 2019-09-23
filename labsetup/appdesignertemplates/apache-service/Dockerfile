FROM alpine
COPY assets /assets
CMD ["cp", "/assets", "/project"]

FROM dockertemplate/interpolator:v0.1.5 as interpolator
COPY assets /assets
