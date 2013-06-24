MvaSnappy
=========

MvaSnappy is a ZF2 module that allow easy to thumbnail, snapshot or PDF generation from a url or a html page using Snappy PHP (5.3+) wrapper for the [wkhtmltopdf][wkhtmltopdf] conversion utility.

Installation
------------
#### With composer

1. Add this project and [MvaSnappy](https://github.com/drigani/MvaSnappy.git) in your composer.json:

    ```json
    "require": {
        "digani/MvaSnappy": "dev-master"
    }
    ```

2. Now tell composer to download MvaSnappy by running the command:

    ```bash
    $ php composer.phar update
    ```

#### Or just clone the repos:
    
    # Install snappy library
    git clone https://github.com/KnpLabs/snappy.git vendor/snappy

    # Install ZF2 Module
    git clone https://github.com/drigani/MvaSnappy.git vendor/digani/MvaSnappy
    

#### Post installation

1. Enabling it in your `application.config.php`file.

    ```php
    <?php
    return array(
        'modules' => array(
            // ...
            'MvaSnappy',            
        ),
        // ...
    );
    ```

Configuration
-------------
After installing MvaSnappy, copy
`./vendor/drigani/MvaSnappy/config/mva-snappy.local.php.dist` to
`./config/autoload/mva-snappy.local.php` and change the binaries path  and add options as desired.


    # /config/autoload/mva-snappy.local.php
```php    
<?php
return array(
    'mva-snappy' => array(
        'pdf' => array(
           'binary'  => '/usr/local/bin/wkhtmltopdf',
           'options' => array(),
        ),   
        'image' => array(
            'binary'  => '/usr/local/bin/wkhtmltoimage',
            'options' => array(),
         )
     )   
);
```

Usage
-----

The module registers two services:  

 - the `mvasnappy.image.service` service allows you to generate images;
 - the `mvasnappy.pdf.service` service allows you to generate pdf files.

### Generate an image from an URL

     $this->serviceLocator->get('mvasnappy.image.service')->generate('http://www.mvlabs.it', '/path/to/myapp/data/image.jpg');

### Generate a pdf document from an URL

     $this->serviceLocator->get('mvasnappy.pdf.service')->generate('http://www.mvlabs.it', '/path/to/myapp/data/document.pdf');
     

### Render a pdf document as response from a controller

```php
    public function testPdfAction() {
    
        $now = new \DateTime();
        $viewRenderer = $this->serviceLocator->get('view_manager')->getRenderer();
         
        $layoutViewModel = $this->layout();
        $layoutViewModel->setTemplate('myModule/myController/layout');
    
        $viewModel = new ViewModel(array(
            'vars' => $vars,            
        ));
    
        $viewModel->setTemplate('myModule/myController/pdf-template');
            
        $layoutViewModel->setVariables(array(
            'content' => $viewRenderer->render($viewModel),
        ));
    
        $htmlOutput = $viewRenderer->render($layoutViewModel);
        
        $output = $this->serviceLocator->get('mvasnappy.pdf.service')->getOutputFromHtml($htmlOutput);
        
        $response = $this->getResponse();
        $headers  = $response->getHeaders();
        $headers->addHeaderLine('Content-Type', 'application/pdf');
        $headers->addHeaderLine('Content-Disposition', "attachment; filename=\"export-" . $now->format('d-m-Y H:i:s') . ".pdf\"");
        
        $response->setContent($output);
        
        return $response;
    
    }    
```    


Credits
-------

MvaSnappy and [Snappy][snappy] are based on the awesome [wkhtmltopdf][wkhtmltopdf].
MvaSnappy has been developed by [mvassociati][mvassociati].

[snappy]: https://github.com/KnpLabs/snappy
[wkhtmltopdf]: http://code.google.com/p/wkhtmltopdf/
[mvassociati]: http://www.mvassociati.it
    
    
