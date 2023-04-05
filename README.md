# Documentation

The documentation website on how to use Vlab Research Platform


## Development

### Running the documentation

You will need to first install [hugo](https://gohugo.io/installation/)

Once installed you can run from the root of the project

```bash
hugo server -D
```

This will start a docuement server on [http://localhost:1313](http://localhost:1313)


### Adding Documentation

To add a new page you can run the following command, replacing the variables 

```bash
# example: 
# hugo new study-configuration/general.md
hugo new $SECTION/$SUBSECTION.md
```

For more information please visit [hugos
documentation](https://gohugo.io)
