# Welcome

So, you've decided to try Codefresh? Welcome on board!

Using this repository we'll help you get up to speed with basic functionality such as: *building Docker images* and use the *shared volumes feature*.

This project uses `Node Js` to build an application which will eventually become a distributable Docker image.

## Looking around

In the root of this repository you'll find a file named `codefresh.yml`, this is our [build descriptor](https://docs.codefresh.io/docs/what-is-the-codefresh-yaml) and it describes the different steps that comprise our process.
Let's quickly review the contents of this file:

### Shared volumes

If you want to share volumes of service in composition step for other yml steps you can use the variable ```${{CF_VOLUME}}```. It will refer to the volume that was generated for the specific flow.
Can be used in conjunction with a composition to provide access to your cloned repository.

```yml
step_file_generation:
    type: composition
    composition:
      version: '2'
      services:
        mysql:
          image: 'mysql:5.6'
          ports:
            - '3306'
          environment:
            MYSQL_ROOT_PASSWORD: root-password
            MYSQL_DATABASE: origination_repository
            MYSQL_USER: or-user
            MYSQL_PASSWORD: or-password
        service-name:
          volumes:
            - ${{CF_VOLUME}}:/codefresh/volume
          image: ${{build_step}}
          command: bash -c "echo hello > /codefresh/volume/myfile.txt"
      volumes:
         ${{CF_VOLUME}}:
           external: true
    composition_candidates:
      test:
        image: ${{build_step}}
        volumes:
          - ${{CF_VOLUME}}:/codefresh/volume
        environment:
          TEST: 'qwerty'
        command: bash -c "cd /codefresh/volume && ls -l && sleep 60 && echo hello"
```

### Building

To bake our application into a Docker image we use Codefresh's [Build step](https://docs.codefresh.io/docs/steps#section-build).

The Build is a simplified abstraction over the Docker build command.

```yml
build_step:
    type: build
    description: UC - build step
    image_name: codefreshio/yaml-example-volumes-compose-step
    dockerfile: Dockerfile
    tag: ${{CF_BRANCH}}
```

Use the `image_name` field to declare the name of the resulting image (don't forget to change the image owner name from `codefreshdemo` to your own!).

## Using This Example

To use this example:

* Fork this repository to your own [INSERT_SCM_SYSTEM (git, bitbucket)] account.
* Log in to Codefresh using your [INSERT_SCM_SYSTEM (git, bitbucket)] account.
* Click the `Add Service` button.
* Select the forked repository.
* Select the `I have a Codefresh.yml file` option.
* Complete the wizard.
* Rejoice!
