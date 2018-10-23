The PiE server was designed, coded, and is maintained by [Robert Cudmore](http://robertcudmore.org).

## History

This project is evolving from 2016 to present. It began as more than three different projects, one for [video recording][trigger-camera2], one for the [home-cage/behavior box][homecage], and another for the [treadmill][treadmill]. The functionality of all these projects have been merged into the current PiE server.
 
## Open Source

The PiE server only exists because of the massive amount of hard-work, creativity, and expertise that has been put in to creating and maintaining a multiplicity of open-source software projects.

"Genius is one percent inspiration, ninety-nine percent perspiration"
   --- [Thomas Edison](https://en.wikiquote.org/wiki/Thomas_Edison)

"Successful Projects Are The Result Of 99% Hard Work, 1% Innovation”
   --- [Linus Torvalds](https://en.wikipedia.org/wiki/Linus_Torvalds)

### Raspberry Pi
 - [Raspberry Pi Foundation](https://www.raspberrypi.org/) - Design, manufacture, and distribute the Raspberry Pi
 - [Raspian](https://www.raspberrypi.org/downloads/raspbian/) - The operating system that runs on the Raspberry Pi
 - [Raspberry GPIO](https://www.raspberrypi.org/documentation/usage/gpio/) - To control and interact with the real world
 - [Pigpio](http://abyz.me.uk/rpi/pigpio/) - A more precise deamon based GPIO interface
 - [PiCamera](https://picamera.readthedocs.io) - Python package to control the Raspberry Pi camera

### Server
  - [Debian](https://www.debian.org/) - Operating system
  - [NGINX](https://www.nginx.com/) - Web server for load balancing, microservices, and API gateways
  - [uwsgi](https://uwsgi-docs.readthedocs.io/en/latest/) - Web standard for NGINX to talk to Python

### [Python](https://www.python.org/) (back-end)
  - [Flask](http://flask.pocoo.org/) - Web microframework
  - [Socketio](https://python-socketio.readthedocs.io) - Bidirectional communication between web browser and Python
  - [SciPy](https://www.scipy.org/) - Scientific computing ecosystem
  - [NumPy](http://www.numpy.org/) - Scientific computing for Python
  - [Pandas](https://pandas.pydata.org/) - Data analysis library
  - [Matplotlib](https://matplotlib.org/) - Plotting
  - Tifffile - General purpose Tiff file library
  - [Redis](https://redis.io/) - Database
  - [Celery](http://www.celeryproject.org/) - Distributed task queue
 
### [Javascript](https://www.javascript.com/) (front-end)
  - [Angular](https://angularjs.org/) - Superheroic Javascript framework
  - [JQuery](https://jquery.com/) - Fast, small, and feature-rich JavaScript library
  - [D3](https://d3js.org/) - Data driven documents
  - [Leaflet](http://leafletjs.com/) - Interactive maps
  - [Plotly](https://plot.ly/) - Modern analytics for the data era
  - [Bootstrap](https://getbootstrap.com/) - Worlds most popular HTML/CSS/JS Toolkit
  
### Containers
  - [Docker][docker] - Software containers are the future

### Programming microcontrollers
 - [PlatformIO][platformio] - An open source ecosystem for IoT development
 
### Documentation
  - [Jekyll](https://jekyllrb.com/) - Static site generator (Main [Map Manager](http://mapmanager.net/) documentation)
  - [mkDocs](http://www.mkdocs.org/) - Static site generator (This Website) using the [Material][material] theme.
  - [Sphinx](http://www.sphinx-doc.org/en/master/) - To create documentation ([PyMapManager API Documentation](http://pymapmanager.readthedocs.io/en/latest/))

### Distribution
  - [Git][git] - Fast version control
  - [Github](https://github.com/) - Software development platform for online storage/sharing/computation
  - [PyPi](https://pypi.org/project/pymapmanager/) - Python package index for online distribution
  - [Travis](https://travis-ci.org/) - Test and Deploy with Confidence

### Online help
 - [Stack Overflow][stackoverflow] - Where Developers Learn, Share, & Build
 
[duckdns]: http://cudmore.duckdns.org
[pymapmanager]: https://github.com/cudmore/PyMapManager
[pymapmanager-data]: https://github.com/mapmanager/PyMapManager-Data
[nginx]: https://www.nginx.com/
[uwsgi]: https://uwsgi-docs.readthedocs.io/en/latest/
[redis]: https://redis.io/
[docker]: https://www.docker.com/community-edition
[treadmill]: https://github.com/cudmore/treadmill
[homecage]: https://github.com/cudmore/homecage
[trigger-camera2]: https://github.com/cudmore/triggercamera2
[material]: https://squidfunk.github.io/mkdocs-material/
[git]: https://git-scm.com/
[platformio]: https://platformio.org/
[stackoverflow]: https://stackoverflow.com/