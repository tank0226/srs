FROM ossrs/srs:dev AS build

# Install depends tools.
RUN yum install -y gcc make gcc-c++ patch unzip perl git

# Build and install SRS.
RUN ./configure --srt=on && make && make install

# Copy all config files for SRS.
COPY conf /usr/local/srs/conf
# Copy the default index.html and srs-console.
RUN cp research/api-server/static-dir/index.html /usr/local/srs/objs/nginx/html/
RUN cp research/api-server/static-dir/favicon.ico /usr/local/srs/objs/nginx/html/
RUN cp research/players/crossdomain.xml /usr/local/srs/objs/nginx/html/
RUN cp -R research/console /usr/local/srs/objs/nginx/html/
RUN cp -R research/players /usr/local/srs/objs/nginx/html/
RUN cp -R 3rdparty/signaling/www/demos /usr/local/srs/objs/nginx/html/

############################################################
# dist
############################################################
FROM centos:7 AS dist

# Expose ports for streaming @see https://github.com/ossrs/srs#ports
EXPOSE 1935 1985 8080 8000/udp 10080/udp

# FFMPEG 4.1
COPY --from=build /usr/local/bin/ffmpeg /usr/local/srs/objs/ffmpeg/bin/ffmpeg
# SRS binary, config files and srs-console.
COPY --from=build /usr/local/srs /usr/local/srs
# Use docker.conf as default config.
COPY conf/docker.conf /usr/local/srs/conf/srs.conf

# Default workdir and command.
WORKDIR /usr/local/srs
CMD ["./objs/srs", "-c", "conf/srs.conf"]
