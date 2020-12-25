
/**
 * 
 * @param {string} componentPath 
 * @param {string} newTagName 
 */
export function useComponent(newTagName, htmlPath, compClass) {
    return fetch(htmlPath)
    .then((res) => {
        return res.text()
    })
    .then((text) => {
        let parser = new DOMParser();
        let doc = parser.parseFromString(text, 'text/html');
        const template = doc.querySelector('template');
        if(!template)
            throw new Error('error: webcomponent DOM element<template> not found.')
        const templateContent = template.content;
        customElements.define(newTagName, compClass);
        let ctor = customElements.get(newTagName);
        let rtnObject = {
            ctor: ctor,
            templateContent: templateContent
        }            
        return rtnObject
    })
}
