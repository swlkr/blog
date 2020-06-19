---
title: AlpineJS Confirm Modals
date: 2020-04-29
---

# Easy alpinejs confirm modals

I was looking for a decent way to define "confirm modals" with alpine, you know those modals that pop up when you're about to do something destructive
to the database, like delete your facebook or instagram account? Those ones.

I stumbled on something pretty bare bones, but it works well:

Here's the css:

    :root {
      --background-body: #202b38;
    }

    .md-modal {
      position: fixed;
      top: 50%;
      left: 50%;
      width: 50%;
      max-width: 630px;
      min-width: 320px;
      margin: 0 auto;
      transform: translate(-50%, -50%);
      z-index: 2;
      background-color: var(--background-body);
    }

    .md-show {
      visibility: visible;
    }

    .md-content {
      padding: 20px;
      transform: scale(0.5);
      opacity: 0;
      transition: all 0.3s;
    }

    .md-show .md-content {
      transform: scale(1);
      opacity: 1;
    }

    .md-overlay {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: 1;
      visibility: hidden;
      opacity: 0;
      background-color: rgba(0,0,0,0.5);
      transition: all 0.3s;
    }

    .md-show.md-overlay {
      opacity: 1;
      visibility: visible;
    }


This could probably be pared down a little bit, but for now (and forever) it works.

Here's some html, well, hiccup, but close enough to accompany that css:


    (defn confirm-modal [request]
      [:div {:class "md-modal" ::class "{'md-show': modalOpen}" :x-show "modalOpen" :@click.away "modalOpen = false"}
      [:div {:class "md-content"}
      [:vstack {:align-x "center"}
       [:h3 "Are you sure?"]
       [:hstack {:spacing "l" :align-x "center"}
        (form request {:method "POST" :x-bind:action "action"}
          [:input {:type "hidden" :name "method" :value "DELETE"}]
          [:button {:type "submit"}
           "Yes, do it"])
        [:a {:href "#" :@click.prevent "modalOpen = false"}
         "No"]]]]])


    (text/html
     (html/doctype :html5)
     [:html {:lang "en" :x-data "app()"}
      [:head
       [:title "ask janet"]
       [:meta {:charset "utf-8"}]
       [:meta {:name "viewport" :content "width=device-width, initial-scale=1"}]
       [:link {:href "/app.css" :rel "stylesheet"}]
       [:script {:src "/alpine.js"}]]
      [:body {:@keydown.escape.prevent "modalOpen = false"}
       [:div "hello world!"]
       [:a {:href "#" :@click.prevent "action = '/delete-account'; modalOpen = true"}]
        "Delete something"
       (confirm-modal request)
       [:div {:class "md-overlay" ::class "{'md-show': modalOpen}"}]
       [:script {:src "/app.js"}]]])))


The final pièce de résistance

    function app() {
      return {
        modalOpen: false,
        action: ''
      };
    }

You know you're making the right decision in your web app when there's only 6 lines of javascript.
