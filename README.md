## PURLs maintained by DCMI

Documents and webpages have 302 redirects for a simple mapping of a PURL to a (document) URL, e.g.:

     http://purl.org/dc/terms/
     => HTTP/1.1 302 Moved Temporarily
     => Location: http://dublincore.org/2012/06/14/dcterms#

Namespace URIs use "partial redirect" PURLs, e.g.:

     http://purl.org/dc/dcam/
     => HTTP/1.1 302 Moved Temporarily
     => Location: http://dublincore.org/2012/06/14/dcam#

...so that

     http://purl.org/dc/dcam/memberOf
     => HTTP/1.1 302 Moved Temporarily
     => Location: http://dublincore.org/2012/06/14/dcam#memberOf

Namespace URIs _minus the slash_ have 302 redirects, e.g.:
     
     http://purl.org/dc/terms
     => HTTP/1.1 302 Moved Temporarily
     => Location: http://purl.org/dc/terms/

The setup for DCMI Metadata Terms:

     http://purl.org/dc/terms/creator 
     => HTTP/1.1 302 Moved Temporarily (based on a partial redirect)
     => Location: http://dublincore.org/2012/06/14/dcterms#creator

At this point, the Apache .htaccess file (see below) does its magic... then:

     http://dublincore.org/2012/06/14/dcterms#creator
     => HTTP/1.1 303 See Other
     => Location: http://dublincore.org/documents/2012/06/14/dcmi-terms/

----------------------------------------------------------------------
www/2012/06/14> cat .htaccess

      # Turn off MultiViews
      Options -MultiViews

      # Directive to ensure *.rdf files served as appropriate content type,
      # if not present in main apache config
      AddType application/rdf+xml .rdf
      AddType text/turtle  .ttl

      <ifModule mod_rewrite.c>
        # Rewrite engine setup
        RewriteEngine On

        # make sure we don't have the file or a directory index to serve
        # the rest fails to redirect if we do
        RewriteCond %{REQUEST_FILENAME} -f [OR]
        RewriteCond %{REQUEST_FILENAME} -d
        RewriteRule .* - [L]

        # Rewrite rule to make sure we serve the turtle content when requested
        RewriteCond %{HTTP_ACCEPT} text/turtle [OR]
        RewriteCond %{HTTP_ACCEPT} application/rdf\+turtle [OR]
        RewriteCond %{HTTP_ACCEPT} application/turtle
        RewriteRule ^(.*)$ %{REQUEST_URI}.ttl [R=303,L]

        # Rewrite rule to make sure we serve the RDF/XML content from the namespace URI by default
        # if we get to here, they're getting RDF/xml
        RewriteCond %{HTTP_ACCEPT} application/rdf\+xml
        RewriteRule ^(.*)$ %{REQUEST_URI}.rdf [R=303,L]

        # Rewrite rule to make sure we serve HTML content from the namespace URI if requested
        RewriteCond %{HTTP_ACCEPT} text/html [OR]
        RewriteCond %{HTTP_ACCEPT} application/xhtml\+xml [OR]
        RewriteCond %{HTTP_USER_AGENT} ^Mozilla/.*
        # redirects all requests for individual vocabs to /dcmi-terms/
        # which should serve /dcmi-terms/index.html
        RewriteRule ^(dcelements)$ HTTP://%{HTTP_HOST}/documents/2012/06/14/dcmi-terms\?v=elements [R=303,L,NE,NC]
        RewriteRule ^(dcterms)$ HTTP://%{HTTP_HOST}/documents/2012/06/14/dcmi-terms\?v=terms [R=303,L,NE,NC]
        RewriteRule ^(dctype)$ HTTP://%{HTTP_HOST}/documents/2012/06/14/dcmi-terms\?v=dcmitype [R=303,L,NE,NC]
        RewriteRule ^(dcam)$ HTTP://%{HTTP_HOST}/documents/2012/06/14/dcmi-terms\?v=dcam [R=303,L,NE,NC]

        # either of these catch-alls means there's never a 404
        # any unservable request will serve dcmi-terms/index.html
        RewriteRule ^(.*)$ HTTP://%{HTTP_HOST}/documents/2012/06/14/dcmi-terms/ [R=303,L,NE]

        #uncomment either the rule above or the rule below to control the default

        # any unservable request will serve RDF Turtle
        #RewriteRule ^(.*)$ %{REQUEST_URI}.ttl [R=303,L]
      </ifModule>

