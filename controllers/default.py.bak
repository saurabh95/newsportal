# -*- coding: utf-8 -*-
# this file is released under public domain and you can use without limitations

#########################################################################
## This is a sample controller
## - index is the default action of any application
## - user is required for authentication and authorization
## - download is for downloading files uploaded in the db (does streaming)
## - call exposes all registered services (none by default)
#########################################################################

@auth.requires_login()
def index():
    """
    example action using the internationalization operator T and flash
    rendered by views/default/index.html or views/generic.html

    if you need a simple wiki simple replace the two lines below with:
    return auth.wiki()
    """
    #response.flash = T("Welcome to web2py!")
    if(auth.user.designation=='A'):
            redirect('admin_page')
    else:
            redirect('user_page')
 #   return dict(message=T('Hello World'))
    return locals()


def user():
    """
    exposes:
    http://..../[app]/default/user/login
    http://..../[app]/default/user/logout
    http://..../[app]/default/user/register
    http://..../[app]/default/user/profile
    http://..../[app]/default/user/retrieve_password
    http://..../[app]/default/user/change_password
    http://..../[app]/default/user/manage_users (requires membership in 
    use @auth.requires_login()
        @auth.requires_membership('group name')
        @auth.requires_permission('read','table name',record_id)
    to decorate functions that need access control
    """
    return dict(form=auth())

@cache.action()
def download():
    """
    allows downloading of uploaded files
    http://..../[app]/default/download/[filename]
    """
    return response.download(request, db)


def call():
    """
    exposes services. for example:
    http://..../[app]/default/call/jsonrpc
    decorate with @services.jsonrpc the functions to expose
    supports xml, json, xmlrpc, jsonrpc, amfrpc, rss, csv
    """
    return service()


@auth.requires_signature()
def data():
    """
    http://..../[app]/default/data/tables
    http://..../[app]/default/data/create/[table]
    http://..../[app]/default/data/read/[table]/[id]
    http://..../[app]/default/data/update/[table]/[id]
    http://..../[app]/default/data/delete/[table]/[id]
    http://..../[app]/default/data/select/[table]
    http://..../[app]/default/data/search/[table]
    but URLs must be signed, i.e. linked with
      A('table',_href=URL('data/tables',user_signature=True))
    or with the signed load operator
      LOAD('default','data.load',args='tables',ajax=True,user_signature=True)
    """
    return dict(form=crud())
@auth.requires_login()    
def admin_page():
        return locals()
@auth.requires_login()    
def user_page():
        return locals()
@auth.requires_login()    
def post_news():
        form=SQLFORM(db.news)
        form.vars.postedby=auth.user.first_name+" "+auth.user.last_name
        if form.accepts(request,session):
                session.flash="Successfully inserted news"
                redirect('index')
                return locals()   
        else:
                session.flash=T("Fill the form correctly")
        session.flash="Successfully inserted news"
        return dict(form=form)
@auth.requires_login()    
def add_category():
        form = SQLFORM(db.category)
        if form.process().accepted:
                print "sdcsdcdscds"
                session.flash="Successfully added new category"
                redirect(URL('index'))
                #return locals()
        elif form.errors:
                print "booba"
        session.flash="Successfully added new category"
               # session.flash=T('Fill the form correctly')
        return dict(form=form)
@auth.requires_login()    
def view_news():
        x=db(db.category.id>0).select(db.category.ALL)
        return dict(x=x)
@auth.requires_login()    
def display():
        category_id=request.vars.cate
        x=db(db.news.category==category_id).select(db.news.ALL,orderby=~(db.news.rating))
        return dict(x=x)
@auth.requires_login()    
def likes():
        news_id=request.vars.nid
        print news_id
        news_id=news_id[:-1]
        print news_id
        x=db((db.nlikes.news_id==news_id)&(auth.user.id==db.nlikes.user_id)).select(db.nlikes.like_number)
        num=db(db.news.id==news_id).select(db.news.likes,db.news.dislikes)
        print "num"
        print news_id
        print "adxasxdasd"
        if(x):
                if(x[0]['like_number']==5):
                        db(db.news.id==news_id).update(likes=num[0]['likes']-1)
                        db((db.nlikes.news_id==news_id)&(auth.user.id==db.nlikes.user_id)).update(like_number=0)
                        session.flash="Unliked"
                elif(x[0]['like_number']==-3):
                        db(db.news.id==news_id).update(likes=num[0]['likes']+1)                            
                        db(db.news.id==news_id).update(dislikes=num[0]['dislikes']-1)
                        db((db.nlikes.news_id==news_id)&(auth.user.id==db.nlikes.user_id)).update(like_number=5)
                        session.flash="Liked"
                else:
                        db(db.news.id==news_id).update(likes=num[0]['likes']+1)
                        db((db.nlikes.news_id==news_id)&(auth.user.id==db.nlikes.user_id)).update(like_number=5)
                        session.flash="Liked"
        else:
                print "nlikes"
                db(db.news.id==news_id).update(likes=num[0]['likes']+1)
                db.nlikes.insert(news_id=news_id,user_id=auth.user.id,like_number=5)
                session.flash="Liked"
        y=db(news_id==db.nlikes.news_id).select(db.nlikes.like_number)
        r=0
        category=db(news_id==db.news.id).select(db.news.category)
        category=category[0]['category']
        st=""
        st=st+"display?cate="+str(category)
        print category
        for i in range(len(y)):
                r+=y[i]['like_number']
        db(news_id==db.news.id).update(rating=100+r)
        
        redirect(st)
        return locals()
        
        
@auth.requires_login()    
def dislikes():
        news_id=request.vars.nid
        print "nid"
        print news_id
        print type(news_id)
        news_id=news_id[:-1]
        x=db((db.nlikes.news_id==news_id)&(auth.user.id==db.nlikes.user_id)).select(db.nlikes.like_number)
        num=db(db.news.id==news_id).select(db.news.likes,db.news.dislikes)
        if(x):
                if(x[0]['like_number']==5):
                        db(db.news.id==news_id).update(likes=num[0]['likes']-1)
                        db(db.news.id==news_id).update(dislikes=num[0]['dislikes']+1)
                        db((db.nlikes.news_id==news_id)&(auth.user.id==db.nlikes.user_id)).update(like_number=-3)
                        session.flash="Disliked"
                        print "1"
                elif(x[0]['like_number']==-3):                          
                        db(db.news.id==news_id).update(dislikes=num[0]['dislikes']-1)
                        db((db.nlikes.news_id==news_id)&(auth.user.id==db.nlikes.user_id)).update(like_number=0)
                        session.flash="Undisliked"
                        print "2"
                else:
                        db(db.news.id==news_id).update(dislikes=num[0]['dislikes']+1)
                        db((db.nlikes.news_id==news_id)&(auth.user.id==db.nlikes.user_id)).update(like_number=-3)
                        session.flash="Disliked"
                        print "3"
        else:
                print "nlikes"
                print "4"
                db(db.news.id==news_id).update(dislikes=num[0]['dislikes']+1)
                db.nlikes.insert(news_id=news_id,user_id=auth.user.id,like_number=-3)
                session.flash="Disliked"
        y=db(db.nlikes.news_id==news_id).select(db.nlikes.like_number)
        r=0
        category=db(news_id==db.news.id).select(db.news.category)
        category=category[0]['category']
        st=""
        st=st+"display?cate="+str(category)
        print category
        for i in range(len(y)):
                r+=y[i]['like_number']
        db(news_id==db.news.id).update(rating=100+r)
        redirect(st)
        return locals()
@auth.requires_login()    
def add_comment():
        commentdata=request.post_vars['com']
        news_id=request.post_vars['news_id']
        print "my luck"
        print news_id
        #news_id=str(news_id)    
        news_id=news_id[:-1]
        db.comments.insert(commentdata=commentdata,user_id=auth.user.id,news_id=news_id)
        category=db(news_id==db.news.id).select(db.news.category)
        category=category[0]['category']
        st=""
        st=st+"display?cate="+str(category)
        session.flash="Successfully posted comment"
        redirect(URL('default',st))
@auth.requires_login()    
def see_comments():
        news_id=request.post_vars['news_id']
        news_id=news_id[:-1]
        x=db(db.comments.news_id==news_id).select(db.comments.commentdata)
        return dict(x=x)
@auth.requires_login()  
def delete_news():
        news_id=request.post_vars['nid']
        news_id=news_id[:-1]
        category=db(news_id==db.news.id).select(db.news.category)
        category=category[0]['category']
        st=""
        st=st+"display?cate="+str(category)
        #news_id=str(news_id)
     #   news_id=news_id.split('/')
     #   news_id=int(news_id[0])
        db(db.news.id==news_id).delete()
        db(db.nlikes.news_id==news_id).delete()
        db(db.comments.news_id==news_id).delete()
        redirect(URL('default',st))
        
@auth.requires_login()  
def edit_news():
        news_id=request.post_vars['nid']
        if news_id is not None:
                session.nid=news_id
        news_id=session.nid
        print "please"
        print news_id
        news_id=news_id[:-1]
        category=db(news_id==db.news.id).select(db.news.category)
        category=category[0]['category']
        st=""
        st=st+"display?cate="+str(category)
        print "categry"
        print category
        form=SQLFORM(db.news,news_id)
        print "FORM"
        if(form.process().accepted):
                session.flash="Successfully edited"
                print "CORRECT"
                redirect(URL('default',st))
        else:
                print "ERRORS"
                session.flash="Form has errors"
        return dict(form=form)

@auth.requires_login()         
def list_user():
        x=db(db.auth_user.designation==None or db.auth_user.designation=='U').select(db.auth_user.ALL)
        return dict(x=x)

@auth.requires_login()   
def delete_user():
        user_id=request.post_vars['user_id']
        user_id=user_id[:-1]
        db(db.auth_user.id==user_id).delete()
        db(db.news.postedby==user_id).delete()
        db(db.comments.user_id==user_id).delete()
        db(db.nlikes.user_id==user_id).delete()
        redirect(URL('default','list_user'))
