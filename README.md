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

----------------------------------------------------------------------
Transition to https://archive.org/services/purl/

    1. http://purl.org/cld           - PETEJOHNSTON
    2. http://purl.org/dc            - TDEHN
    3. http://purl.org/dcevents      - DCMI
    4. http://purl.org/dcpapers      - TKAC
    5. http://purl.org/dcregistry    - JTENNIS
    6. http://purl.org/dcx           - DCMI
    7. http://purl.org/metadata      - DCMI
    8. http://purl.org/net/tombaker  - tombaker

I was expecting:

* 1-7 now to be owned by 'DCMI PURL Administrator', associated with IA
  account purl@dublincore.net

* 8 now to be owned by 'thomasbaker', associated with IA account
  tom@tombaker.org

As far as I can tell, all of the owners listed above (PETEJOHNSTON...)
correspond to purl.org logins used by the old Zepheira software --
including the owner DCMI, which (as far as I can tell) is no longer
usable by DCMI for logging into archive.org and thus to administer the
PURLs.

Do I perhaps simply not understand how authentication works on 
the new system?

Many thanks and best regards,
Tom Baker

On Sun, Nov 27, 2016 at 09:49:37AM -0800, Mark Graham wrote:
> Hi Thomas,
> 
> Done!
> 
> Please let me know if you have more issues/requests. 
> 
> Onward,
> 
> - Mark Graham
> Director, Wayback Machine, Internet Archive
> (917) 697-0110
> 
> > On Nov 27, 2016, at 7:21 AM, Thomas Baker <tom@tombaker.org> wrote:
> > 
> > On Wed, Nov 23, 2016 at 02:45:16PM -0800, Mark Graham wrote:
> >> Thank you for getting back with us Thomas.
> >> 
> >> Looking forward to hearing back from you after your conf. 
> >> 
> >> Take care,
> >> 
> >> - Mark Graham
> >> Director, Wayback Machine, Internet Archive
> >> (917) 697-0110
> > 
> > Hi Mark!
> > 
> > We have activated the new email address
> > 
> >    purl@dublincore.net
> > 
> > and used it to set up an IA account.  The name associated with 
> > the account is "DCMI PURL Administrator".  This account should 
> > please be used as the owner for the following PURL domains:
> > 
> >    http://purl.org/cld
> >    http://purl.org/dc
> >    http://purl.org/dcevents
> >    http://purl.org/dcpapers
> >    http://purl.org/dcregistry
> >    http://purl.org/dcx
> >    http://purl.org/metadata
> > 
> > While you are re-setting those owners, I would much appreciate if you
> > could reset the owner for my personal PURL, which points to a
> > now-defunct website.  The purl:
> > 
> >    http://purl.org/net/tombaker
> > 
> > should be associated with tom@tombaker.org.
> > 
> > Many thanks and best regards,
> > Tom
> > 
> > -- 
> > Tom Baker <tom@tombaker.org>
> 
> 
> 
> 

-- 
Tom Baker <tom@tombaker.org>


