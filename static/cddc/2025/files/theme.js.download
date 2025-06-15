/*
Name: 			Theme Base
Written by: 	Okler Themes - (http://www.okler.net)
Theme Version:	9.6.0
*/

// Theme
window.theme = {};

// Theme Common Functions
window.theme.fn = {

	getOptions: function(opts) {

		if (typeof(opts) == 'object') {

			return opts;

		} else if (typeof(opts) == 'string') {

			try {
				return JSON.parse(opts.replace(/'/g, '"').replace(';', ''));
			} catch (e) {
				return {};
			}

		} else {

			return {};

		}

	},

	execPluginFunction: function(functionName, context) {
		var args = Array.prototype.slice.call(arguments, 2);
		var namespaces = functionName.split(".");
		var func = namespaces.pop();

		for (var i = 0; i < namespaces.length; i++) {
			context = context[namespaces[i]];
		}

		return context[func].apply(context, args);
	},

	intObs: function(selector, functionName, intObsOptions, alwaysObserve) {
		var $el = document.querySelectorAll(selector);
		var intersectionObserverOptions = {
			rootMargin: '0px 0px 200px 0px'
		}

		if (Object.keys(intObsOptions).length) {
			intersectionObserverOptions = $.extend(intersectionObserverOptions, intObsOptions);
		}

		var observer = new IntersectionObserver(function(entries) {
			for (var i = 0; i < entries.length; i++) {
				var entry = entries[i];

				if (entry.intersectionRatio > 0) {
					if (typeof functionName === 'string') {
						var func = Function('return ' + functionName)();
					} else {
						var callback = functionName;

						callback.call($(entry.target));
					}

					// Unobserve
					if (!alwaysObserve) {
						observer.unobserve(entry.target);
					}

				}
			}
		}, intersectionObserverOptions);

		$($el).each(function() {
			observer.observe($(this)[0]);
		});
	},

	intObsInit: function(selector, functionName) {
		var $el = document.querySelectorAll(selector);
		var intersectionObserverOptions = {
			rootMargin: '200px'
		}

		var observer = new IntersectionObserver(function(entries) {
			for (var i = 0; i < entries.length; i++) {
				var entry = entries[i];
				if (entry.intersectionRatio > 0) {

					var $this = $(entry.target),
						opts;

					var pluginOptions = theme.fn.getOptions($this.data('plugin-options'));
					if (pluginOptions)
						opts = pluginOptions;

					theme.fn.execPluginFunction(functionName, $this, opts);

					// Unobserve
					observer.unobserve(entry.target);
				}
			}
		}, intersectionObserverOptions);

		$($el).each(function() {
			observer.observe($(this)[0]);
		});
	},

	dynIntObsInit: function(selector, functionName, pluginDefaults) {
		var $el = document.querySelectorAll(selector);

		$($el).each(function() {
			var $this = $(this),
				opts;

			var pluginOptions = theme.fn.getOptions($this.data('plugin-options'));
			if (pluginOptions)
				opts = pluginOptions;

			var mergedPluginDefaults = theme.fn.mergeOptions(pluginDefaults, opts)

			var intersectionObserverOptions = {
				rootMargin: theme.fn.getRootMargin(functionName, mergedPluginDefaults),
				threshold: 0
			}

			if (!mergedPluginDefaults.forceInit) {

				var observer = new IntersectionObserver(function(entries) {
					for (var i = 0; i < entries.length; i++) {
						var entry = entries[i];

						if (entry.intersectionRatio > 0) {
							theme.fn.execPluginFunction(functionName, $this, mergedPluginDefaults);

							// Unobserve
							observer.unobserve(entry.target);
						}
					}
				}, intersectionObserverOptions);

				observer.observe($this[0]);

			} else {
				theme.fn.execPluginFunction(functionName, $this, mergedPluginDefaults);
			}
		});
	},

	getRootMargin: function(plugin, pluginDefaults) {
		switch (plugin) {
			case 'themePluginCounter':
				return pluginDefaults.accY ? '0px 0px ' + pluginDefaults.accY + 'px 0px' : '0px 0px 200px 0px';
				break;

			case 'themePluginAnimate':
				return pluginDefaults.accY ? '0px 0px ' + pluginDefaults.accY + 'px 0px' : '0px 0px 200px 0px';
				break;

			case 'themePluginIcon':
				return pluginDefaults.accY ? '0px 0px ' + pluginDefaults.accY + 'px 0px' : '0px 0px 200px 0px';
				break;

			case 'themePluginRandomImages':
				return pluginDefaults.accY ? '0px 0px ' + pluginDefaults.accY + 'px 0px' : '0px 0px 200px 0px';
				break;

			default:
				return '0px 0px 200px 0px';
				break;
		}
	},

	mergeOptions: function(obj1, obj2) {
		var obj3 = {};

		for (var attrname in obj1) {
			obj3[attrname] = obj1[attrname];
		}
		for (var attrname in obj2) {
			obj3[attrname] = obj2[attrname];
		}

		return obj3;
	},

	execOnceTroughEvent: function($el, event, callback) {
		var self = this,
			dataName = self.formatDataName(event);

		$($el).on(event, function() {
			if (!$(this).data(dataName)) {

				// Exec Callback Function
				callback.call($(this));

				// Add data name 
				$(this).data(dataName, true);

				// Unbind event
				$(this).off(event);
			}
		});

		return this;
	},

	execOnceTroughWindowEvent: function($el, event, callback) {
		var self = this,
			dataName = self.formatDataName(event);

		$($el).on(event, function() {
			if (!$(this).data(dataName)) {

				// Exec Callback Function
				callback();

				// Add data name 
				$(this).data(dataName, true);

				// Unbind event
				$(this).off(event);
			}
		});

		return this;
	},

	formatDataName: function(name) {
		name = name.replace('.', '');
		return name;
	},

	isElementInView: function($el) {
		var rect = $el[0].getBoundingClientRect();

		return (
			rect.top <= (window.innerHeight / 3)
		);
	},

	getScripts: function(arr, path) {
		var _arr = $.map(arr, function(scr) {
			return $.getScript((path || "") + scr);
		});

		_arr.push($.Deferred(function(deferred) {
			$(deferred.resolve);
		}));

		return $.when.apply($, _arr);
	}

};

// Local Environment Warning
try {
	$( document ).ready(function() {
		if ("file://" === location.origin) {
			if ($('[data-icon]').length || $('iframe').length) {
				$('.modalLocalEnvironment').remove();
				$('body').append('<div class="modal fade" id="modalLocalEnvironment" aria-hidden="true" aria-labelledby="exampleModalToggleLabel" tabindex="-1"><div class="modal-dialog modal-dialog-centered"><div class="modal-content"><div class="modal-header"><h5 class="modal-title" id="exampleModalToggleLabel">Local Environment Warning</h5><button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button></div><div class="modal-body">SVG Objects, Icons, YouTube and Vimeo Videos might not show correctly on local environment. For better result, please preview on a server.</div><div class="modal-footer"><button type="button" class="btn btn-primary" data-bs-dismiss="modal">Close</button></div></div></div></div>');

				var modalLocalEnvironment = document.getElementById('modalLocalEnvironment');
				var modalLocalEnvironment = bootstrap.Modal.getOrCreateInstance(modalLocalEnvironment);
				modalLocalEnvironment.show();
			}
		}
	});
} catch (e) {}

/**
 * Copyright 2016 Google Inc. All Rights Reserved.
 *
 * Licensed under the W3C SOFTWARE AND DOCUMENT NOTICE AND LICENSE.
 *
 *  https://www.w3.org/Consortium/Legal/2015/copyright-software-and-document
 *
 */
!function(){"use strict";if("object"==typeof window)if("IntersectionObserver"in window&&"IntersectionObserverEntry"in window&&"intersectionRatio"in window.IntersectionObserverEntry.prototype)"isIntersecting"in window.IntersectionObserverEntry.prototype||Object.defineProperty(window.IntersectionObserverEntry.prototype,"isIntersecting",{get:function(){return this.intersectionRatio>0}});else{var t=function(t){for(var e=window.document,o=i(e);o;)o=i(e=o.ownerDocument);return e}(),e=[],o=null,n=null;s.prototype.THROTTLE_TIMEOUT=100,s.prototype.POLL_INTERVAL=null,s.prototype.USE_MUTATION_OBSERVER=!0,s._setupCrossOriginUpdater=function(){return o||(o=function(t,o){n=t&&o?l(t,o):{top:0,bottom:0,left:0,right:0,width:0,height:0},e.forEach(function(t){t._checkForIntersections()})}),o},s._resetCrossOriginUpdater=function(){o=null,n=null},s.prototype.observe=function(t){if(!this._observationTargets.some(function(e){return e.element==t})){if(!t||1!=t.nodeType)throw new Error("target must be an Element");this._registerInstance(),this._observationTargets.push({element:t,entry:null}),this._monitorIntersections(t.ownerDocument),this._checkForIntersections()}},s.prototype.unobserve=function(t){this._observationTargets=this._observationTargets.filter(function(e){return e.element!=t}),this._unmonitorIntersections(t.ownerDocument),0==this._observationTargets.length&&this._unregisterInstance()},s.prototype.disconnect=function(){this._observationTargets=[],this._unmonitorAllIntersections(),this._unregisterInstance()},s.prototype.takeRecords=function(){var t=this._queuedEntries.slice();return this._queuedEntries=[],t},s.prototype._initThresholds=function(t){var e=t||[0];return Array.isArray(e)||(e=[e]),e.sort().filter(function(t,e,o){if("number"!=typeof t||isNaN(t)||t<0||t>1)throw new Error("threshold must be a number between 0 and 1 inclusively");return t!==o[e-1]})},s.prototype._parseRootMargin=function(t){var e=(t||"0px").split(/\s+/).map(function(t){var e=/^(-?\d*\.?\d+)(px|%)$/.exec(t);if(!e)throw new Error("rootMargin must be specified in pixels or percent");return{value:parseFloat(e[1]),unit:e[2]}});return e[1]=e[1]||e[0],e[2]=e[2]||e[0],e[3]=e[3]||e[1],e},s.prototype._monitorIntersections=function(e){var o=e.defaultView;if(o&&-1==this._monitoringDocuments.indexOf(e)){var n=this._checkForIntersections,r=null,s=null;this.POLL_INTERVAL?r=o.setInterval(n,this.POLL_INTERVAL):(h(o,"resize",n,!0),h(e,"scroll",n,!0),this.USE_MUTATION_OBSERVER&&"MutationObserver"in o&&(s=new o.MutationObserver(n)).observe(e,{attributes:!0,childList:!0,characterData:!0,subtree:!0})),this._monitoringDocuments.push(e),this._monitoringUnsubscribes.push(function(){var t=e.defaultView;t&&(r&&t.clearInterval(r),c(t,"resize",n,!0)),c(e,"scroll",n,!0),s&&s.disconnect()});var u=this.root&&(this.root.ownerDocument||this.root)||t;if(e!=u){var a=i(e);a&&this._monitorIntersections(a.ownerDocument)}}},s.prototype._unmonitorIntersections=function(e){var o=this._monitoringDocuments.indexOf(e);if(-1!=o){var n=this.root&&(this.root.ownerDocument||this.root)||t;if(!this._observationTargets.some(function(t){var o=t.element.ownerDocument;if(o==e)return!0;for(;o&&o!=n;){var r=i(o);if((o=r&&r.ownerDocument)==e)return!0}return!1})){var r=this._monitoringUnsubscribes[o];if(this._monitoringDocuments.splice(o,1),this._monitoringUnsubscribes.splice(o,1),r(),e!=n){var s=i(e);s&&this._unmonitorIntersections(s.ownerDocument)}}}},s.prototype._unmonitorAllIntersections=function(){var t=this._monitoringUnsubscribes.slice(0);this._monitoringDocuments.length=0,this._monitoringUnsubscribes.length=0;for(var e=0;e<t.length;e++)t[e]()},s.prototype._checkForIntersections=function(){if(this.root||!o||n){var t=this._rootIsInDom(),e=t?this._getRootRect():{top:0,bottom:0,left:0,right:0,width:0,height:0};this._observationTargets.forEach(function(n){var i=n.element,s=u(i),h=this._rootContainsTarget(i),c=n.entry,a=t&&h&&this._computeTargetAndRootIntersection(i,s,e),l=null;this._rootContainsTarget(i)?o&&!this.root||(l=e):l={top:0,bottom:0,left:0,right:0,width:0,height:0};var f=n.entry=new r({time:window.performance&&performance.now&&performance.now(),target:i,boundingClientRect:s,rootBounds:l,intersectionRect:a});c?t&&h?this._hasCrossedThreshold(c,f)&&this._queuedEntries.push(f):c&&c.isIntersecting&&this._queuedEntries.push(f):this._queuedEntries.push(f)},this),this._queuedEntries.length&&this._callback(this.takeRecords(),this)}},s.prototype._computeTargetAndRootIntersection=function(e,i,r){if("none"!=window.getComputedStyle(e).display){for(var s,h,c,a,f,d,g,m,v=i,_=p(e),b=!1;!b&&_;){var w=null,y=1==_.nodeType?window.getComputedStyle(_):{};if("none"==y.display)return null;if(_==this.root||9==_.nodeType)if(b=!0,_==this.root||_==t)o&&!this.root?!n||0==n.width&&0==n.height?(_=null,w=null,v=null):w=n:w=r;else{var I=p(_),E=I&&u(I),T=I&&this._computeTargetAndRootIntersection(I,E,r);E&&T?(_=I,w=l(E,T)):(_=null,v=null)}else{var R=_.ownerDocument;_!=R.body&&_!=R.documentElement&&"visible"!=y.overflow&&(w=u(_))}if(w&&(s=w,h=v,c=void 0,a=void 0,f=void 0,d=void 0,g=void 0,m=void 0,c=Math.max(s.top,h.top),a=Math.min(s.bottom,h.bottom),f=Math.max(s.left,h.left),d=Math.min(s.right,h.right),m=a-c,v=(g=d-f)>=0&&m>=0&&{top:c,bottom:a,left:f,right:d,width:g,height:m}||null),!v)break;_=_&&p(_)}return v}},s.prototype._getRootRect=function(){var e;if(this.root&&!d(this.root))e=u(this.root);else{var o=d(this.root)?this.root:t,n=o.documentElement,i=o.body;e={top:0,left:0,right:n.clientWidth||i.clientWidth,width:n.clientWidth||i.clientWidth,bottom:n.clientHeight||i.clientHeight,height:n.clientHeight||i.clientHeight}}return this._expandRectByRootMargin(e)},s.prototype._expandRectByRootMargin=function(t){var e=this._rootMarginValues.map(function(e,o){return"px"==e.unit?e.value:e.value*(o%2?t.width:t.height)/100}),o={top:t.top-e[0],right:t.right+e[1],bottom:t.bottom+e[2],left:t.left-e[3]};return o.width=o.right-o.left,o.height=o.bottom-o.top,o},s.prototype._hasCrossedThreshold=function(t,e){var o=t&&t.isIntersecting?t.intersectionRatio||0:-1,n=e.isIntersecting?e.intersectionRatio||0:-1;if(o!==n)for(var i=0;i<this.thresholds.length;i++){var r=this.thresholds[i];if(r==o||r==n||r<o!=r<n)return!0}},s.prototype._rootIsInDom=function(){return!this.root||f(t,this.root)},s.prototype._rootContainsTarget=function(e){var o=this.root&&(this.root.ownerDocument||this.root)||t;return f(o,e)&&(!this.root||o==e.ownerDocument)},s.prototype._registerInstance=function(){e.indexOf(this)<0&&e.push(this)},s.prototype._unregisterInstance=function(){var t=e.indexOf(this);-1!=t&&e.splice(t,1)},window.IntersectionObserver=s,window.IntersectionObserverEntry=r}function i(t){try{return t.defaultView&&t.defaultView.frameElement||null}catch(t){return null}}function r(t){this.time=t.time,this.target=t.target,this.rootBounds=a(t.rootBounds),this.boundingClientRect=a(t.boundingClientRect),this.intersectionRect=a(t.intersectionRect||{top:0,bottom:0,left:0,right:0,width:0,height:0}),this.isIntersecting=!!t.intersectionRect;var e=this.boundingClientRect,o=e.width*e.height,n=this.intersectionRect,i=n.width*n.height;this.intersectionRatio=o?Number((i/o).toFixed(4)):this.isIntersecting?1:0}function s(t,e){var o,n,i,r=e||{};if("function"!=typeof t)throw new Error("callback must be a function");if(r.root&&1!=r.root.nodeType&&9!=r.root.nodeType)throw new Error("root must be a Document or Element");this._checkForIntersections=(o=this._checkForIntersections.bind(this),n=this.THROTTLE_TIMEOUT,i=null,function(){i||(i=setTimeout(function(){o(),i=null},n))}),this._callback=t,this._observationTargets=[],this._queuedEntries=[],this._rootMarginValues=this._parseRootMargin(r.rootMargin),this.thresholds=this._initThresholds(r.threshold),this.root=r.root||null,this.rootMargin=this._rootMarginValues.map(function(t){return t.value+t.unit}).join(" "),this._monitoringDocuments=[],this._monitoringUnsubscribes=[]}function h(t,e,o,n){"function"==typeof t.addEventListener?t.addEventListener(e,o,n||!1):"function"==typeof t.attachEvent&&t.attachEvent("on"+e,o)}function c(t,e,o,n){"function"==typeof t.removeEventListener?t.removeEventListener(e,o,n||!1):"function"==typeof t.detatchEvent&&t.detatchEvent("on"+e,o)}function u(t){var e;try{e=t.getBoundingClientRect()}catch(t){}return e?(e.width&&e.height||(e={top:e.top,right:e.right,bottom:e.bottom,left:e.left,width:e.right-e.left,height:e.bottom-e.top}),e):{top:0,bottom:0,left:0,right:0,width:0,height:0}}function a(t){return!t||"x"in t?t:{top:t.top,y:t.top,bottom:t.bottom,left:t.left,x:t.left,right:t.right,width:t.width,height:t.height}}function l(t,e){var o=e.top-t.top,n=e.left-t.left;return{top:o,left:n,height:e.height,width:e.width,bottom:o+e.height,right:n+e.width}}function f(t,e){for(var o=e;o;){if(o==t)return!0;o=p(o)}return!1}function p(e){var o=e.parentNode;return 9==e.nodeType&&e!=t?i(e):(o&&o.assignedSlot&&(o=o.assignedSlot.parentNode),o&&11==o.nodeType&&o.host?o.host:o)}function d(t){return t&&9===t.nodeType}}();

(function($) {
	$.extend({

		browserSelector: function() {

			// jQuery.browser.mobile (http://detectmobilebrowser.com/)
			(function(a){(jQuery.browser=jQuery.browser||{}).mobile=/(android|bb\d+|meego).+mobile|avantgo|bada\/|blackberry|blazer|compal|elaine|fennec|hiptop|iemobile|ip(hone|od)|iris|kindle|lge |maemo|midp|mmp|mobile.+firefox|netfront|opera m(ob|in)i|palm( os)?|phone|p(ixi|re)\/|plucker|pocket|psp|series(4|6)0|symbian|treo|up\.(browser|link)|vodafone|wap|windows (ce|phone)|xda|xiino/i.test(a)||/1207|6310|6590|3gso|4thp|50[1-6]i|770s|802s|a wa|abac|ac(er|oo|s\-)|ai(ko|rn)|al(av|ca|co)|amoi|an(ex|ny|yw)|aptu|ar(ch|go)|as(te|us)|attw|au(di|\-m|r |s )|avan|be(ck|ll|nq)|bi(lb|rd)|bl(ac|az)|br(e|v)w|bumb|bw\-(n|u)|c55\/|capi|ccwa|cdm\-|cell|chtm|cldc|cmd\-|co(mp|nd)|craw|da(it|ll|ng)|dbte|dc\-s|devi|dica|dmob|do(c|p)o|ds(12|\-d)|el(49|ai)|em(l2|ul)|er(ic|k0)|esl8|ez([4-7]0|os|wa|ze)|fetc|fly(\-|_)|g1 u|g560|gene|gf\-5|g\-mo|go(\.w|od)|gr(ad|un)|haie|hcit|hd\-(m|p|t)|hei\-|hi(pt|ta)|hp( i|ip)|hs\-c|ht(c(\-| |_|a|g|p|s|t)|tp)|hu(aw|tc)|i\-(20|go|ma)|i230|iac( |\-|\/)|ibro|idea|ig01|ikom|im1k|inno|ipaq|iris|ja(t|v)a|jbro|jemu|jigs|kddi|keji|kgt( |\/)|klon|kpt |kwc\-|kyo(c|k)|le(no|xi)|lg( g|\/(k|l|u)|50|54|\-[a-w])|libw|lynx|m1\-w|m3ga|m50\/|ma(te|ui|xo)|mc(01|21|ca)|m\-cr|me(rc|ri)|mi(o8|oa|ts)|mmef|mo(01|02|bi|de|do|t(\-| |o|v)|zz)|mt(50|p1|v )|mwbp|mywa|n10[0-2]|n20[2-3]|n30(0|2)|n50(0|2|5)|n7(0(0|1)|10)|ne((c|m)\-|on|tf|wf|wg|wt)|nok(6|i)|nzph|o2im|op(ti|wv)|oran|owg1|p800|pan(a|d|t)|pdxg|pg(13|\-([1-8]|c))|phil|pire|pl(ay|uc)|pn\-2|po(ck|rt|se)|prox|psio|pt\-g|qa\-a|qc(07|12|21|32|60|\-[2-7]|i\-)|qtek|r380|r600|raks|rim9|ro(ve|zo)|s55\/|sa(ge|ma|mm|ms|ny|va)|sc(01|h\-|oo|p\-)|sdk\/|se(c(\-|0|1)|47|mc|nd|ri)|sgh\-|shar|sie(\-|m)|sk\-0|sl(45|id)|sm(al|ar|b3|it|t5)|so(ft|ny)|sp(01|h\-|v\-|v )|sy(01|mb)|t2(18|50)|t6(00|10|18)|ta(gt|lk)|tcl\-|tdg\-|tel(i|m)|tim\-|t\-mo|to(pl|sh)|ts(70|m\-|m3|m5)|tx\-9|up(\.b|g1|si)|utst|v400|v750|veri|vi(rg|te)|vk(40|5[0-3]|\-v)|vm40|voda|vulc|vx(52|53|60|61|70|80|81|83|85|98)|w3c(\-| )|webc|whit|wi(g |nc|nw)|wmlb|wonu|x700|yas\-|your|zeto|zte\-/i.test(a.substr(0,4))})(navigator.userAgent||navigator.vendor||window.opera);

			// Touch
			var hasTouch = 'ontouchstart' in window || navigator.msMaxTouchPoints;

			var u = navigator.userAgent,
				ua = u.toLowerCase(),
				is = function (t) {
					return ua.indexOf(t) > -1;
				},
				g = 'gecko',
				w = 'webkit',
				s = 'safari',
				o = 'opera',
				h = document.documentElement,
				b = [(!(/opera|webtv/i.test(ua)) && /msie\s(\d)/.test(ua)) ? ('ie ie' + parseFloat(navigator.appVersion.split("MSIE")[1])) : is('firefox/2') ? g + ' ff2' : is('firefox/3.5') ? g + ' ff3 ff3_5' : is('firefox/3') ? g + ' ff3' : is('gecko/') ? g : is('opera') ? o + (/version\/(\d+)/.test(ua) ? ' ' + o + RegExp.jQuery1 : (/opera(\s|\/)(\d+)/.test(ua) ? ' ' + o + RegExp.jQuery2 : '')) : is('konqueror') ? 'konqueror' : is('chrome') ? w + ' chrome' : is('iron') ? w + ' iron' : is('applewebkit/') ? w + ' ' + s + (/version\/(\d+)/.test(ua) ? ' ' + s + RegExp.jQuery1 : '') : is('mozilla/') ? g : '', is('j2me') ? 'mobile' : is('iphone') ? 'iphone' : is('ipod') ? 'ipod' : is('mac') ? 'mac' : is('darwin') ? 'mac' : is('webtv') ? 'webtv' : is('win') ? 'win' : is('freebsd') ? 'freebsd' : (is('x11') || is('linux')) ? 'linux' : '', 'js'];

			c = b.join(' ');

			if ($.browser.mobile) {
				c += ' mobile';
			}

			if (hasTouch) {
				c += ' touch';
			}

			h.className += ' ' + c;

			// Edge Detect
			var isEdge = /Edge/.test(navigator.userAgent);

			if(isEdge) {
				$('html').removeClass('chrome').addClass('edge');
			}

			// IE11 Detect
			var isIE11 = !(window.ActiveXObject) && "ActiveXObject" in window;

			if (isIE11) {
				$('html').removeClass('gecko').addClass('ie ie11');
				return;
			}

			// Dark and Boxed Compatibility
			if($('body').hasClass('dark')) {
				$('html').addClass('dark');
			}

			if($('body').hasClass('boxed')) {
				$('html').addClass('boxed');
			}

		}

	});

	$.browserSelector();

	/*
	Global Variable For Screen Size
	*/
	theme.globalWindowWidth = $(window).width();
	var globalWindowWidth = $(window).width();
	window.onresize = function() {
		theme.globalWindowWidth = $(window).width();
	}

	/*
	Browser Workarounds
	*/
	if (/iPad|iPhone|iPod/.test(navigator.platform)) {

		// iPad/Iphone/iPod Hover Workaround
		$(document).ready(function($) {
			$('.thumb-info').attr('onclick', 'return true');
		});
	}

	/*
	Tabs
	*/
	if( $('a[data-bs-toggle="tab"]').length ) {
		$('a[data-bs-toggle="tab"]').on('shown.bs.tab', function (e) {
			var $tabPane = $($(e.target).attr('href'));

			// Carousel Refresh
			if($tabPane.length) {
				$tabPane.find('.owl-carousel').trigger('refresh.owl.carousel');
			}

			// Change Active Class
			$(this).parents('.nav-tabs').find('.active').removeClass('active');
			$(this).addClass('active').parent().addClass('active');
		});	

		if( window.location.hash ) {
			$(window).on('load', function(){
				if( window.location.hash !== '*' && $( window.location.hash ).get(0) ) {
					new bootstrap.Tab( $('a.nav-link[href="'+ window.location.hash +'"]:not([data-hash])')[0] ).show();
				}
			});
		}
	}

	/*
	On Load Scroll
	*/
	if( !$('html').hasClass('disable-onload-scroll') && window.location.hash && !['#*'].includes( window.location.hash ) ) {

		window.scrollTo(0, 0);

		$(window).on('load', function() {
			setTimeout(function() {

				var target = window.location.hash,
					offset = ( $(window).width() < 768 ) ? 180 : 90;

				if (!$(target).length) {
					return;
				}

				if ( $("a[href$='" + window.location.hash + "']").is('[data-hash-offset]') ) {
					offset = parseInt( $("a[href$='" + window.location.hash + "']").first().attr('data-hash-offset') );
				} else if ( $("html").is('[data-hash-offset]') ) {
					offset = parseInt( $("html").attr('data-hash-offset') );
				}

				if (isNaN(offset)) {
					offset = 0;
				}

				$('body').addClass('scrolling');

				$('html, body').animate({
					scrollTop: $(target).offset().top - offset
				}, 600, 'easeOutQuad', function() {
					$('body').removeClass('scrolling');
				});

			}, 1);
		});
	}

	/*
	* Text Rotator
	*/
	$.fn.extend({
		textRotator: function(options) {

			var defaults = {
				fadeSpeed: 500,
				pauseSpeed: 100,
				child: null
			};

			var options = $.extend(defaults, options);

			return this.each(function() {
				var o = options;
				var obj = $(this);
				var items = $(obj.children(), obj);
				items.each(function() {
					$(this).hide();
				})
				if (!o.child) {
					var next = $(obj).children(':first');
				} else {
					var next = o.child;
				}
				$(next).fadeIn(o.fadeSpeed, function() {
					$(next).delay(o.pauseSpeed).fadeOut(o.fadeSpeed, function() {
						var next = $(this).next();
						if (next.length == 0) {
							next = $(obj).children(':first');
						}
						$(obj).textRotator({
							child: next,
							fadeSpeed: o.fadeSpeed,
							pauseSpeed: o.pauseSpeed
						});
					})
				});
			});
		}
	});

	/*
	* Notice Top bar
	*/
	var $noticeTopBar = {
		$wrapper: $('.notice-top-bar'),
		$closeBtn: $('.notice-top-bar-close'),
		$header: $('#header'),
		$body: $('.body'),
		init: function() {
			var self = this;

			if( !$.cookie('portoNoticeTopBarClose') ) {
				self
					.build()
					.events();
			} else {
				self.$wrapper.parent().prepend( '<!-- Notice Top Bar removed by cookie -->' );
				self.$wrapper.remove();
			}

			return this;
		},
		build: function(){
			var self = this;

			$(window).on('load', function(){
				setTimeout(function(){
					self.$body.css({
						'margin-top': self.$wrapper.outerHeight(),
						'transition': 'ease margin 300ms'
					});

					$('#noticeTopBarContent').textRotator({
						fadeSpeed: 500,
						pauseSpeed: 5000
					});

					if( ['absolute', 'fixed'].includes( self.$header.css('position') ) ) {
						self.$header.css({
							'top': self.$wrapper.outerHeight(),
							'transition': 'ease top 300ms'
						});
					}

					$(window).trigger('notice.top.bar.opened');

				}, 1000);
			});

			return this;
		},
		events: function() {
			var self = this;

			self.$closeBtn.on('click', function(e){
				e.preventDefault();

				self.$body.animate({
					'margin-top': 0,
				}, 300, function(){
					self.$wrapper.remove();
					self.saveCookie();
				});

				if( ['absolute', 'fixed'].includes( self.$header.css('position') ) ) {
					self.$header.animate({
						top: 0
					}, 300);
				}

				// When header has shrink effect
				if( self.$header.hasClass('header-effect-shrink') ) {
					self.$header.find('.header-body').animate({
						top: 0
					}, 300);
				}

				$(window).trigger('notice.top.bar.closed');
			});

			return this;
		},
		checkCookie: function(){
			var self = this;

			if( $.cookie('portoNoticeTopBarClose') ) {
				return true;
			} else {
				return false;
			}

			return this;
		},
		saveCookie: function() {
			var self = this;

			$.cookie('portoNoticeTopBarClose', true);

			return this;
		}
	}

	if( $('.notice-top-bar').length ) {
		$noticeTopBar.init();
	}

	/*
	* Image Hotspots
	*/
	if( $('.image-hotspot').length ) {
		$('.image-hotspot')
			.append('<span class="ring"></span>')
			.append('<span class="circle"></span>');
	}

	/*
	* Page Transition
	*/
	if( $('body[data-plugin-page-transition]').length ) {
		
		var link_click = false;

		$(document).on('click', 'a', function(e){
			link_click = $(this);
		});

		$(window).on("beforeunload", function(e) {
			if( typeof link_click === 'object' ) {
				var href = link_click.attr('href');

				if( href.indexOf('mailto:') != 0 && href.indexOf('tel:') != 0 && !link_click.data('rm-from-transition') ) {
					$('body').addClass('page-transition-active');
				}
			}
		});

		$(window).on("pageshow", function(e){
			if( e.persisted ) {
				if( $('html').hasClass('safari') ) {
					window.location.reload();
				}
				
		        $('body').removeClass('page-transition-active');
		    }
		});
	}

	/*
	* Thumb Info Floating Caption
	*/
	$('.thumb-info-floating-caption').each(function() {

		$(this)
			.addClass('thumb-info-floating-element-wrapper')
			.append( '<span class="thumb-info-floating-element thumb-info-floating-caption-title d-none">'+ $(this).data('title') +'</span>' );

		if( $(this).data('type') ) {
			$('.thumb-info-floating-caption-title', $(this))
				.append( '<div class="thumb-info-floating-caption-type">'+ $(this).data('type') +'</div>' )
				.css({
					'padding-bottom' : 22
				});
		}

		if( $(this).hasClass('thumb-info-floating-caption-clean') ) {
			$('.thumb-info-floating-element', $(this)).addClass('bg-transparent');
		}

	});

	/*
	* Thumb Info Floating Element
	*/
	if( $('.thumb-info-floating-element-wrapper').length ) {

		$('.thumb-info-floating-element-wrapper').on('mouseenter', function(e){
			
			if(!$(this).data('offset')) {
				$(this).data('offset', 0);
			}

			var offset = parseInt($(this).data('offset'));

			$('.thumb-info-floating-element-clone').remove();

			$('.thumb-info-floating-element', $(this)).clone().addClass('thumb-info-floating-element-clone p-fixed p-events-none').attr('style', 'transform: scale(0.1);').removeClass('d-none').appendTo('body');

			$('.thumb-info-floating-element-clone').css({
				left: e.clientX + (offset),
				top: e.clientY + (offset)
			}).fadeIn(300);

			gsap.to('.thumb-info-floating-element-clone', 0.5, {
				css: {
					scaleX: 1,
					scaleY: 1
				}
			});

			$(document).off('mousemove').on('mousemove', function(e){

				gsap.to('.thumb-info-floating-element-clone', 0.5, {
					css: {
						left: e.clientX + (offset),
						top: e.clientY + (offset)
					}
				});

			});

		}).on('mouseout', function(){

			gsap.to('.thumb-info-floating-element-clone', 0.5, {
				css: {
					scaleX: 0.5,
					scaleY: 0.5,
					opacity: 0
				}
			});

		});

	}

	/*
	* Thumb Info Direction Aware
	*/
	$(window).on('load', function() {
		$('.thumb-info-wrapper-direction-aware').each( function() {
			$(this).hoverdir({
				speed : 300,
				easing : 'ease',
				hoverDelay : 0,
				inverse : false,
				hoverElem: '.thumb-info-wrapper-overlay'
			});
		});
	});

	/*
	* Toggle Text Click
	*/
	$('[data-toggle-text-click]').on('click', function () {
		$(this).text(function(i, text){
			return text === $(this).attr('data-toggle-text-click') ? $(this).attr('data-toggle-text-click-alt') : $(this).attr('data-toggle-text-click');
		});
	});

	/*
	* Shape Divider Aspect Ratio
	*/
	if( $('.shape-divider').length ) {
		aspectRatioSVG();
		$(window).on('resize', function(){
			aspectRatioSVG();
		});
	}

	/*
	* Shape Divider Animated
	*/
	if( $('.shape-divider-horizontal-animation').length ) {
		theme.fn.intObs('.shape-divider-horizontal-animation', function(){
			for( var i = 0; i <= 1; i++ ) {
				var svgClone = $(this).find('svg:nth-child(1)').clone();

				$(this).append( svgClone )
			}

			$(this).addClass('start');
		}, {});
	}

	/*
	* Toggle Class
	*/
	$('[data-porto-toggle-class]').on('click', function (e) {
		e.preventDefault();

		$(this).toggleClass( $(this).data('porto-toggle-class') );
	});

	/*
	* Dynamic Height
	*/
	var $window = $(window);
	$window.on('resize dynamic.height.resize', function(){
		$('[data-dynamic-height]').each(function(){
			var $this = $(this),
				values = JSON.parse($this.data('dynamic-height').replace(/'/g,'"').replace(';',''))

			// XS
			if( $window.width() < 576 ) {
				$this.height( values[4] );
			}

			// SM
			if( $window.width() > 575 && $window.width() < 768 ) {
				$this.height( values[3] );
			}

			// MD
			if( $window.width() > 767 && $window.width() < 992 ) {
				$this.height( values[2] );
			}

			// LG
			if( $window.width() > 991 && $window.width() < 1200 ) {
				$this.height( values[1] );
			}

			// XS
			if( $window.width() > 1199 ) {
				$this.height( values[0] );
			}
		});
	});

	// Mobile First Load
	if( $window.width() < 992 ) {
		$window.trigger('dynamic.height.resize');
	}

	/* Video - Trigger Play */
	if( $('[data-trigger-play-video]').length ) {
		theme.fn.execOnceTroughEvent( '[data-trigger-play-video]', 'mouseover.trigger.play.video', function(){
			var $video = $( $(this).data('trigger-play-video') );

			$(this).on('click', function(e){
				e.preventDefault();

				if( $(this).data('trigger-play-video-remove') == 'yes' ) {
					$(this).animate({
						opacity: 0
					}, 300, function(){
						$video[0].play();

						$(this).remove();
					});
				} else {
					setTimeout(function(){
						$video[0].play();
					},300);
				}
			});
		});
	}

	/* Video - Custom Auto Play */
	if( $('video[data-auto-play]').length ) {
		$(window).on('load', function(){
			$('video[data-auto-play]').each(function(){
				var $video = $(this);

				setTimeout(function(){
					if( $( '#' + $video.attr('id') ).length ) {
						if( $( '[data-trigger-play-video="#' + $video.attr('id') + '"]' ).data('trigger-play-video-remove') == 'yes' ) {
							$( '[data-trigger-play-video="#' + $video.attr('id') + '"]' ).animate({
								opacity: 0
							}, 300, function(){
								$video[0].play();

								$( '[data-trigger-play-video="#' + $video.attr('id') + '"]' ).remove();
							});
						} else {
							setTimeout(function(){
								$video[0].play();
							},300);
						}
					}
				}, 100);

			});
		});
	}

	/*
	* Remove min height after the load of page
	*/
	if( $('[data-remove-min-height]').length ) {
		$(window).on('load', function(){
			$('[data-remove-min-height]').each(function(){
				$(this).css({
					'min-height': 0
				});
			});
		});
	}

	/*
	* Style Switcher Open Loader Button
	*/
	if( $('.style-switcher-open-loader').length ) {

		$('.style-switcher-open-loader').on('click', function(e){
			e.preventDefault();

			var $this = $(this);

			// Add Spinner to icon
			$this.addClass('style-switcher-open-loader-loading');

			var	basePath = $(this).data('base-path'),
				skinSrc = $(this).data('skin-src');

			var script1 = document.createElement("script");
		  	script1.src = basePath + "master/style-switcher/style.switcher.localstorage.js";

		  	var script2 = document.createElement("script");
		  	script2.src = basePath + "master/style-switcher/style.switcher.js";
		  	script2.id = "styleSwitcherScript";
		  	script2.setAttribute('data-base-path', basePath);
		  	script2.setAttribute('data-skin-src', skinSrc);

		  	script2.onload = function() {
		  		setTimeout(function(){
		  			// Trigger a click to open the style switcher sidebar
			  		function checkIfReady() {
					    if( !$('.style-switcher-open').length ) {
					       window.setTimeout(checkIfReady, 100);
					    } else {
					      $('.style-switcher-open').trigger('click');
					    }
					}
					checkIfReady();

		  		}, 500);
		  	}

		  	document.body.appendChild(script1);
		  	document.body.appendChild(script2);	

		});

	}

	})(jQuery);

/*
* Scroll and Focus
*/
function scrollAndFocus($this, scrollTarget, focusTarget, scrollOffset, scrollAgain) {
	(function($) {

		$('body').addClass('scrolling');

		// if it's inside a header menu
		if( $($this).closest('#mainNav').length ) {
			$($this).parents('.collapse.show').collapse('hide');
		}

		$('html, body').animate({
			scrollTop: $(scrollTarget).offset().top - (scrollOffset ? scrollOffset : 0)
		}, 300, function() {
			$('body').removeClass('scrolling');

			setTimeout(function(){
				$(focusTarget).focus();
			}, 500);

			if( scrollAgain ) {
				$('html, body').animate({
					scrollTop: $(scrollTarget).offset().top - (scrollOffset ? scrollOffset : 0)
				});
			}
		});
	})(jQuery);
}

/*
* Shape Divider - SVG Aspect Ratio
*/
function aspectRatioSVG() {
	if( $(window).width() < 1950 ) {
		$('.shape-divider svg[preserveAspectRatio]').each(function(){
			if( !$(this).parent().hasClass('shape-divider-horizontal-animation') ) {
				$(this).attr('preserveAspectRatio', 'xMinYMin');
			} else {
				$(this).attr('preserveAspectRatio', 'none');
			}
		});
	} else {
		$('.shape-divider svg[preserveAspectRatio]').each(function(){
			$(this).attr('preserveAspectRatio', 'none');
		});
	}
}

/*
* Lazy Load Background Images (with lazySizes plugin)
*/
document.addEventListener('lazybeforeunveil', function(e){
    var bg = e.target.getAttribute('data-bg-src');
    if(bg) {
        e.target.style.backgroundImage = 'url(' + bg + ')';
    }
});

(function (factory) {
    if (typeof define === 'function' && define.amd) {
        // AMD
        define(['jquery'], factory);
    } else if (typeof exports === 'object') {
        // CommonJS
        factory(require('jquery'));
    } else {
        // Browser globals
        factory(jQuery);
    }
}(function ($) {
  var CountTo = function (element, options) {
    this.$element = $(element);
    this.options  = $.extend({}, CountTo.DEFAULTS, this.dataOptions(), options);
    this.init();
  };

  CountTo.DEFAULTS = {
    from: 0,               // the number the element should start at
    to: 0,                 // the number the element should end at
    speed: 1000,           // how long it should take to count between the target numbers
    refreshInterval: 100,  // how often the element should be updated
    decimals: 0,           // the number of decimal places to show
    formatter: formatter,  // handler for formatting the value before rendering
    onUpdate: null,        // callback method for every time the element is updated
    onComplete: null       // callback method for when the element finishes updating
  };

  CountTo.prototype.init = function () {
    this.value     = this.options.from;
    this.loops     = Math.ceil(this.options.speed / this.options.refreshInterval);
    this.loopCount = 0;
    this.increment = (this.options.to - this.options.from) / this.loops;
  };

  CountTo.prototype.dataOptions = function () {
    var options = {
      from:            this.$element.data('from'),
      to:              this.$element.data('to'),
      speed:           this.$element.data('speed'),
      refreshInterval: this.$element.data('refresh-interval'),
      decimals:        this.$element.data('decimals')
    };

    var keys = Object.keys(options);

    for (var i in keys) {
      var key = keys[i];

      if (typeof(options[key]) === 'undefined') {
        delete options[key];
      }
    }

    return options;
  };

  CountTo.prototype.update = function () {
    this.value += this.increment;
    this.loopCount++;

    this.render();

    if (typeof(this.options.onUpdate) == 'function') {
      this.options.onUpdate.call(this.$element, this.value);
    }

    if (this.loopCount >= this.loops) {
      clearInterval(this.interval);
      this.value = this.options.to;

      if (typeof(this.options.onComplete) == 'function') {
        this.options.onComplete.call(this.$element, this.value);
      }
    }
  };

  CountTo.prototype.render = function () {
    var formattedValue = this.options.formatter.call(this.$element, this.value, this.options);
    this.$element.text(formattedValue);
  };

  CountTo.prototype.restart = function () {
    this.stop();
    this.init();
    this.start();
  };

  CountTo.prototype.start = function () {
    this.stop();
    this.render();
    this.interval = setInterval(this.update.bind(this), this.options.refreshInterval);
  };

  CountTo.prototype.stop = function () {
    if (this.interval) {
      clearInterval(this.interval);
    }
  };

  CountTo.prototype.toggle = function () {
    if (this.interval) {
      this.stop();
    } else {
      this.start();
    }
  };

  function formatter(value, options) {
    return value.toFixed(options.decimals);
  }

  $.fn.countTo = function (option) {
    return this.each(function () {
      var $this   = $(this);
      var data    = $this.data('countTo');
      var init    = !data || typeof(option) === 'object';
      var options = typeof(option) === 'object' ? option : {};
      var method  = typeof(option) === 'string' ? option : 'start';

      if (init) {
        if (data) data.stop();
        $this.data('countTo', data = new CountTo(this, options));
      }

      data[method].call(data);
    });
  };
}));


(function($){

    /**
     * Copyright 2012, Digital Fusion
     * Licensed under the MIT license.
     * http://teamdf.com/jquery-plugins/license/
     *
     * @author Sam Sehnert
     * @desc A small plugin that checks whether elements are within
     *       the user visible viewport of a web browser.
     *       only accounts for vertical position, not horizontal.
     */
    $.fn.visible = function(partial,hidden,direction,container){

        if (this.length < 1)
            return;

        var $t          = this.length > 1 ? this.eq(0) : this,
						isContained = typeof container !== 'undefined' && container !== null,
						$w				  = isContained ? $(container) : $(window),
						wPosition        = isContained ? $w.position() : 0,
            t           = $t.get(0),
            vpWidth     = $w.outerWidth(),
            vpHeight    = $w.outerHeight(),
            direction   = (direction) ? direction : 'both',
            clientSize  = hidden === true ? t.offsetWidth * t.offsetHeight : true;

        if (typeof t.getBoundingClientRect === 'function'){

            // Use this native browser method, if available.
            var rec = t.getBoundingClientRect(),
                tViz = isContained ?
												rec.top - wPosition.top >= 0 && rec.top < vpHeight + wPosition.top :
												rec.top >= 0 && rec.top < vpHeight,
                bViz = isContained ?
												rec.bottom - wPosition.top > 0 && rec.bottom <= vpHeight + wPosition.top :
												rec.bottom > 0 && rec.bottom <= vpHeight,
                lViz = isContained ?
												rec.left - wPosition.left >= 0 && rec.left < vpWidth + wPosition.left :
												rec.left >= 0 && rec.left <  vpWidth,
                rViz = isContained ?
												rec.right - wPosition.left > 0  && rec.right < vpWidth + wPosition.left  :
												rec.right > 0 && rec.right <= vpWidth,
                vVisible   = partial ? tViz || bViz : tViz && bViz,
                hVisible   = partial ? lViz || rViz : lViz && rViz;

            if(direction === 'both')
                return clientSize && vVisible && hVisible;
            else if(direction === 'vertical')
                return clientSize && vVisible;
            else if(direction === 'horizontal')
                return clientSize && hVisible;
        } else {

            var viewTop 				= isContained ? 0 : wPosition,
                viewBottom      = viewTop + vpHeight,
                viewLeft        = $w.scrollLeft(),
                viewRight       = viewLeft + vpWidth,
                position          = $t.position(),
                _top            = position.top,
                _bottom         = _top + $t.height(),
                _left           = position.left,
                _right          = _left + $t.width(),
                compareTop      = partial === true ? _bottom : _top,
                compareBottom   = partial === true ? _top : _bottom,
                compareLeft     = partial === true ? _right : _left,
                compareRight    = partial === true ? _left : _right;

            if(direction === 'both')
                return !!clientSize && ((compareBottom <= viewBottom) && (compareTop >= viewTop)) && ((compareRight <= viewRight) && (compareLeft >= viewLeft));
            else if(direction === 'vertical')
                return !!clientSize && ((compareBottom <= viewBottom) && (compareTop >= viewTop));
            else if(direction === 'horizontal')
                return !!clientSize && ((compareRight <= viewRight) && (compareLeft >= viewLeft));
        }
    };

})(jQuery);


/**
* jquery-match-height 0.7.2 by @liabru
* http://brm.io/jquery-match-height/
* License: MIT
*/

;(function(factory) { // eslint-disable-line no-extra-semi
    'use strict';
    if (typeof define === 'function' && define.amd) {
        // AMD
        define(['jquery'], factory);
    } else if (typeof module !== 'undefined' && module.exports) {
        // CommonJS
        module.exports = factory(require('jquery'));
    } else {
        // Global
        factory(jQuery);
    }
})(function($) {
    /*
    *  internal
    */

    var _previousResizeWidth = -1,
        _updateTimeout = -1;

    /*
    *  _parse
    *  value parse utility function
    */

    var _parse = function(value) {
        // parse value and convert NaN to 0
        return parseFloat(value) || 0;
    };

    /*
    *  _rows
    *  utility function returns array of jQuery selections representing each row
    *  (as displayed after float wrapping applied by browser)
    */

    var _rows = function(elements) {
        var tolerance = 1,
            $elements = $(elements),
            lastTop = null,
            rows = [];

        // group elements by their top position
        $elements.each(function(){
            var $that = $(this),
                top = $that.offset().top - _parse($that.css('margin-top')),
                lastRow = rows.length > 0 ? rows[rows.length - 1] : null;

            if (lastRow === null) {
                // first item on the row, so just push it
                rows.push($that);
            } else {
                // if the row top is the same, add to the row group
                if (Math.floor(Math.abs(lastTop - top)) <= tolerance) {
                    rows[rows.length - 1] = lastRow.add($that);
                } else {
                    // otherwise start a new row group
                    rows.push($that);
                }
            }

            // keep track of the last row top
            lastTop = top;
        });

        return rows;
    };

    /*
    *  _parseOptions
    *  handle plugin options
    */

    var _parseOptions = function(options) {
        var opts = {
            byRow: true,
            property: 'height',
            target: null,
            remove: false
        };

        if (typeof options === 'object') {
            return $.extend(opts, options);
        }

        if (typeof options === 'boolean') {
            opts.byRow = options;
        } else if (options === 'remove') {
            opts.remove = true;
        }

        return opts;
    };

    /*
    *  matchHeight
    *  plugin definition
    */

    var matchHeight = $.fn.matchHeight = function(options) {
        var opts = _parseOptions(options);

        // handle remove
        if (opts.remove) {
            var that = this;

            // remove fixed height from all selected elements
            this.css(opts.property, '');

            // remove selected elements from all groups
            $.each(matchHeight._groups, function(key, group) {
                group.elements = group.elements.not(that);
            });

            // TODO: cleanup empty groups

            return this;
        }

        if (this.length <= 1 && !opts.target) {
            return this;
        }

        // keep track of this group so we can re-apply later on load and resize events
        matchHeight._groups.push({
            elements: this,
            options: opts
        });

        // match each element's height to the tallest element in the selection
        matchHeight._apply(this, opts);

        return this;
    };

    /*
    *  plugin global options
    */

    matchHeight.version = '0.7.2';
    matchHeight._groups = [];
    matchHeight._throttle = 80;
    matchHeight._maintainScroll = false;
    matchHeight._beforeUpdate = null;
    matchHeight._afterUpdate = null;
    matchHeight._rows = _rows;
    matchHeight._parse = _parse;
    matchHeight._parseOptions = _parseOptions;

    /*
    *  matchHeight._apply
    *  apply matchHeight to given elements
    */

    matchHeight._apply = function(elements, options) {
        var opts = _parseOptions(options),
            $elements = $(elements),
            rows = [$elements];

        // take note of scroll position
        var scrollTop = $(window).scrollTop(),
            htmlHeight = $('html').outerHeight(true);

        // get hidden parents
        var $hiddenParents = $elements.parents().filter(':hidden');

        // cache the original inline style
        $hiddenParents.each(function() {
            var $that = $(this);
            $that.data('style-cache', $that.attr('style'));
        });

        // temporarily must force hidden parents visible
        $hiddenParents.css('display', 'block');

        // get rows if using byRow, otherwise assume one row
        if (opts.byRow && !opts.target) {

            // must first force an arbitrary equal height so floating elements break evenly
            $elements.each(function() {
                var $that = $(this),
                    display = $that.css('display');

                // temporarily force a usable display value
                if (display !== 'inline-block' && display !== 'flex' && display !== 'inline-flex') {
                    display = 'block';
                }

                // cache the original inline style
                $that.data('style-cache', $that.attr('style'));

                $that.css({
                    'display': display,
                    'padding-top': '0',
                    'padding-bottom': '0',
                    'margin-top': '0',
                    'margin-bottom': '0',
                    'border-top-width': '0',
                    'border-bottom-width': '0',
                    'height': '100px',
                    'overflow': 'hidden'
                });
            });

            // get the array of rows (based on element top position)
            rows = _rows($elements);

            // revert original inline styles
            $elements.each(function() {
                var $that = $(this);
                $that.attr('style', $that.data('style-cache') || '');
            });
        }

        $.each(rows, function(key, row) {
            var $row = $(row),
                targetHeight = 0;

            if (!opts.target) {
                // skip apply to rows with only one item
                if (opts.byRow && $row.length <= 1) {
                    $row.css(opts.property, '');
                    return;
                }

                // iterate the row and find the max height
                $row.each(function(){
                    var $that = $(this),
                        style = $that.attr('style'),
                        display = $that.css('display');

                    // temporarily force a usable display value
                    if (display !== 'inline-block' && display !== 'flex' && display !== 'inline-flex') {
                        display = 'block';
                    }

                    // ensure we get the correct actual height (and not a previously set height value)
                    var css = { 'display': display };
                    css[opts.property] = '';
                    $that.css(css);

                    // find the max height (including padding, but not margin)
                    if ($that.outerHeight(false) > targetHeight) {
                        targetHeight = $that.outerHeight(false);
                    }

                    // revert styles
                    if (style) {
                        $that.attr('style', style);
                    } else {
                        $that.css('display', '');
                    }
                });
            } else {
                // if target set, use the height of the target element
                targetHeight = opts.target.outerHeight(false);
            }

            // iterate the row and apply the height to all elements
            $row.each(function(){
                var $that = $(this),
                    verticalPadding = 0;

                // don't apply to a target
                if (opts.target && $that.is(opts.target)) {
                    return;
                }

                // handle padding and border correctly (required when not using border-box)
                if ($that.css('box-sizing') !== 'border-box') {
                    verticalPadding += _parse($that.css('border-top-width')) + _parse($that.css('border-bottom-width'));
                    verticalPadding += _parse($that.css('padding-top')) + _parse($that.css('padding-bottom'));
                }

                // set the height (accounting for padding and border)
                $that.css(opts.property, (targetHeight - verticalPadding) + 'px');
            });
        });

        // revert hidden parents
        $hiddenParents.each(function() {
            var $that = $(this);
            $that.attr('style', $that.data('style-cache') || null);
        });

        // restore scroll position if enabled
        if (matchHeight._maintainScroll) {
            $(window).scrollTop((scrollTop / htmlHeight) * $('html').outerHeight(true));
        }

        return this;
    };

    /*
    *  matchHeight._applyDataApi
    *  applies matchHeight to all elements with a data-match-height attribute
    */

    matchHeight._applyDataApi = function() {
        var groups = {};

        // generate groups by their groupId set by elements using data-match-height
        $('[data-match-height], [data-mh]').each(function() {
            var $this = $(this),
                groupId = $this.attr('data-mh') || $this.attr('data-match-height');

            if (groupId in groups) {
                groups[groupId] = groups[groupId].add($this);
            } else {
                groups[groupId] = $this;
            }
        });

        // apply matchHeight to each group
        $.each(groups, function() {
            this.matchHeight(true);
        });
    };

    /*
    *  matchHeight._update
    *  updates matchHeight on all current groups with their correct options
    */

    var _update = function(event) {
        if (matchHeight._beforeUpdate) {
            matchHeight._beforeUpdate(event, matchHeight._groups);
        }

        $.each(matchHeight._groups, function() {
            matchHeight._apply(this.elements, this.options);
        });

        if (matchHeight._afterUpdate) {
            matchHeight._afterUpdate(event, matchHeight._groups);
        }
    };

    matchHeight._update = function(throttle, event) {
        // prevent update if fired from a resize event
        // where the viewport width hasn't actually changed
        // fixes an event looping bug in IE8
        if (event && event.type === 'resize') {
            var windowWidth = $(window).width();
            if (windowWidth === _previousResizeWidth) {
                return;
            }
            _previousResizeWidth = windowWidth;
        }

        // throttle updates
        if (!throttle) {
            _update(event);
        } else if (_updateTimeout === -1) {
            _updateTimeout = setTimeout(function() {
                _update(event);
                _updateTimeout = -1;
            }, matchHeight._throttle);
        }
    };

    /*
    *  bind events
    */

    // apply on DOM ready event
    $(matchHeight._applyDataApi);

    // use on or bind where supported
    var on = $.fn.on ? 'on' : 'bind';

    // update heights on load and resize events
    $(window)[on]('load', function(event) {
        matchHeight._update(false, event);
    });

    // throttled update heights on resize events
    $(window)[on]('resize orientationchange', function(event) {
        matchHeight._update(true, event);
    });

});


/*! waitForImages jQuery Plugin - v2.4.0 - 2018-02-13
* https://github.com/alexanderdickson/waitForImages
* Copyright (c) 2018 Alex Dickson; Licensed MIT */
;(function (factory) {
    if (typeof define === 'function' && define.amd) {
        // AMD. Register as an anonymous module.
        define(['jquery'], factory);
    } else if (typeof exports === 'object') {
        // CommonJS / nodejs module
        module.exports = factory(require('jquery'));
    } else {
        // Browser globals
        factory(jQuery);
    }
}(function ($) {
    // Namespace all events.
    var eventNamespace = 'waitForImages';

    // Is srcset supported by this browser?
    var hasSrcset = (function(img) {
        return img.srcset && img.sizes;
    })(new Image());

    // CSS properties which contain references to images.
    $.waitForImages = {
        hasImageProperties: [
            'backgroundImage',
            'listStyleImage',
            'borderImage',
            'borderCornerImage',
            'cursor'
        ],
        hasImageAttributes: ['srcset']
    };

    // Custom selector to find all `img` elements with a valid `src` attribute.
    $.expr.pseudos['has-src'] = function (obj) {
        // Ensure we are dealing with an `img` element with a valid
        // `src` attribute.
        return $(obj).is('img[src][src!=""]');
    };

    // Custom selector to find images which are not already cached by the
    // browser.
    $.expr.pseudos.uncached = function (obj) {
        // Ensure we are dealing with an `img` element with a valid
        // `src` attribute.
        if (!$(obj).is(':has-src')) {
            return false;
        }

        return !obj.complete;
    };

    $.fn.waitForImages = function () {

        var allImgsLength = 0;
        var allImgsLoaded = 0;
        var deferred = $.Deferred();
        var originalCollection = this;
        var allImgs = [];

        // CSS properties which may contain an image.
        var hasImgProperties = $.waitForImages.hasImageProperties || [];
        // Element attributes which may contain an image.
        var hasImageAttributes = $.waitForImages.hasImageAttributes || [];
        // To match `url()` references.
        // Spec: http://www.w3.org/TR/CSS2/syndata.html#value-def-uri
        var matchUrl = /url\(\s*(['"]?)(.*?)\1\s*\)/g;

        var finishedCallback;
        var eachCallback;
        var waitForAll;

        // Handle options object (if passed).
        if ($.isPlainObject(arguments[0])) {

            waitForAll = arguments[0].waitForAll;
            eachCallback = arguments[0].each;
            finishedCallback = arguments[0].finished;

        } else {

            // Handle if using deferred object and only one param was passed in.
            if (arguments.length === 1 && $.type(arguments[0]) === 'boolean') {
                waitForAll = arguments[0];
            } else {
                finishedCallback = arguments[0];
                eachCallback = arguments[1];
                waitForAll = arguments[2];
            }

        }

        // Handle missing callbacks.
        finishedCallback = finishedCallback || $.noop;
        eachCallback = eachCallback || $.noop;

        // Convert waitForAll to Boolean.
        waitForAll = !! waitForAll;

        // Ensure callbacks are functions.
        if (!$.isFunction(finishedCallback) || !$.isFunction(eachCallback)) {
            throw new TypeError('An invalid callback was supplied.');
        }

        this.each(function () {
            // Build a list of all imgs, dependent on what images will
            // be considered.
            var obj = $(this);

            if (waitForAll) {

                // Get all elements (including the original), as any one of
                // them could have a background image.
                obj.find('*').addBack().each(function () {
                    var element = $(this);

                    // If an `img` element, add it. But keep iterating in
                    // case it has a background image too.
                    if (element.is('img:has-src') &&
                        !element.is('[srcset]')) {
                        allImgs.push({
                            src: element.attr('src'),
                            element: element[0]
                        });
                    }

                    $.each(hasImgProperties, function (i, property) {
                        var propertyValue = element.css(property);
                        var match;

                        // If it doesn't contain this property, skip.
                        if (!propertyValue) {
                            return true;
                        }

                        // Get all url() of this element.
                        while (match = matchUrl.exec(propertyValue)) {
                            allImgs.push({
                                src: match[2],
                                element: element[0]
                            });
                        }
                    });

                    $.each(hasImageAttributes, function (i, attribute) {
                        var attributeValue = element.attr(attribute);
                        var attributeValues;

                        // If it doesn't contain this property, skip.
                        if (!attributeValue) {
                            return true;
                        }

                        allImgs.push({
                            src: element.attr('src'),
                            srcset: element.attr('srcset'),
                            element: element[0]
                        });
                    });
                });
            } else {
                // For images only, the task is simpler.
                obj.find('img:has-src')
                    .each(function () {
                    allImgs.push({
                        src: this.src,
                        element: this
                    });
                });
            }
        });

        allImgsLength = allImgs.length;
        allImgsLoaded = 0;

        // If no images found, don't bother.
        if (allImgsLength === 0) {
            finishedCallback.call(originalCollection);
            deferred.resolveWith(originalCollection);
        }

        // Now that we've found all imgs in all elements in this,
        // load them and attach callbacks.
        $.each(allImgs, function (i, img) {

            var image = new Image();
            var events =
              'load.' + eventNamespace + ' error.' + eventNamespace;

            // Handle the image loading and error with the same callback.
            $(image).one(events, function me (event) {
                // If an error occurred with loading the image, set the
                // third argument accordingly.
                var eachArguments = [
                    allImgsLoaded,
                    allImgsLength,
                    event.type == 'load'
                ];
                allImgsLoaded++;

                eachCallback.apply(img.element, eachArguments);
                deferred.notifyWith(img.element, eachArguments);

                // Unbind the event listeners. I use this in addition to
                // `one` as one of those events won't be called (either
                // 'load' or 'error' will be called).
                $(this).off(events, me);

                if (allImgsLoaded == allImgsLength) {
                    finishedCallback.call(originalCollection[0]);
                    deferred.resolveWith(originalCollection[0]);
                    return false;
                }

            });

            if (hasSrcset && img.srcset) {
                image.srcset = img.srcset;
                image.sizes = img.sizes;
            }
            image.src = img.src;
        });

        return deferred.promise();

    };
}));


/* jQuery-FontSpy.js v3.0.0
 * https://github.com/patrickmarabeas/jQuery-FontSpy.js
 *
 * Copyright 2013, Patrick Marabeas http://pulse-dev.com
 * Released under the MIT license
 * http://opensource.org/licenses/mit-license.php
 *
 * Date: 02/11/2015
 */

(function( w, $ ) {

  fontSpy = function  ( fontName, conf ) {
    var $html = $('html'),
        $body = $('body'),
        fontFamilyName = fontName;

        // Throw error if fontName is not a string or not is left as an empty string
        if (typeof fontFamilyName !== 'string' || fontFamilyName === '') {
          throw 'A valid fontName is required. fontName must be a string and must not be an empty string.';
        }

    var defaults = {
        font: fontFamilyName,
        fontClass: fontFamilyName.toLowerCase().replace( /\s/g, '' ),
        success: function() {},
        failure: function() {},
        testFont: 'Courier New',
        testString: 'QW@HhsXJ',
        glyphs: '',
        delay: 50,
        timeOut: 1000,
        callback: $.noop
    };

    var config = $.extend( defaults, conf );

    var $tester = $('<span>' + config.testString+config.glyphs + '</span>')
        .css('position', 'absolute')
        .css('top', '-9999px')
        .css('left', '-9999px')
        .css('visibility', 'hidden')
        .css('fontFamily', config.testFont)
        .css('fontSize', '250px');

    $body.append($tester);

    var fallbackFontWidth = $tester.outerWidth();

    $tester.css('fontFamily', config.font + ',' + config.testFont);

    var failure = function () {
      $html.addClass("no-"+config.fontClass);
      if( config && config.failure ) {
        config.failure();
      }
      config.callback(new Error('FontSpy timeout'));
      $tester.remove();
    };

    var success = function () {
      config.callback();
      $html.addClass(config.fontClass);
      if( config && config.success ) {
        config.success();
      }
      $tester.remove();
    };

    var retry = function () {
      setTimeout(checkFont, config.delay);
      config.timeOut = config.timeOut - config.delay;
    };

    var checkFont = function () {
      var loadedFontWidth = $tester.outerWidth();

      if (fallbackFontWidth !== loadedFontWidth){
        success();
      } else if(config.timeOut < 0) {
        failure();
      } else {
        retry();
      }
    }

    checkFont();
    }
  })( this, jQuery );


/*
Plugin Name: 	jQuery.pin
https://github.com/webpop/jquery.pin
Licensed under the terms of the MIT license.
*/
(function ($) {
	"use strict";
	$.fn.pin = function (options) {
		var scrollY = 0, elements = [], disabled = false, $window = $(window);

		options = options || {};

		var recalculateLimits = function () {
			for (var i=0, len=elements.length; i<len; i++) {
				var $this = elements[i];

				if (options.minWidth && $window.outerWidth() <= options.minWidth) {
					if ($this.parent().is(".pin-wrapper")) { $this.unwrap(); }
					$this.css({width: "", left: "", top: "", position: ""});
					if (options.activeClass) { $this.removeClass(options.activeClass); }
					disabled = true;
					continue;
				} else {
					disabled = false;
				}

				var $container = options.containerSelector ? $this.closest(options.containerSelector) : $(document.body);
				var offset = $this.offset();
				var containerOffset = $container.offset();
				var parentOffset = $this.parent().offset();

				if (!$this.parent().is(".pin-wrapper")) {
					$this.wrap("<div class='pin-wrapper'>");
				}

				var pad = $.extend({
				  top: 0,
				  bottom: 0
				}, options.padding || {});

				$this.data("pin", {
					pad: pad,
					from: (options.containerSelector ? containerOffset.top : offset.top) - pad.top,
					to: containerOffset.top + $container.height() - $this.outerHeight() - pad.bottom,
					end: containerOffset.top + $container.height(),
					parentTop: parentOffset.top
				});

				$this.css({width: $this.outerWidth()});
				$this.parent().css("height", $this.outerHeight());
			}
		};

		var onScroll = function () {
			if (disabled) { return; }

			scrollY = $window.scrollTop();

			var elmts = [];
			for (var i=0, len=elements.length; i<len; i++) {          
				var $this = $(elements[i]),
					data  = $this.data("pin");

				if (!data) { // Removed element
				  continue;
				}

				elmts.push($this); 
				  
				var from = data.from - data.pad.bottom,
					to = data.to - data.pad.top;
			  
				if (from + $this.outerHeight() > data.end) {
					$this.css('position', '');
					continue;
				}
			  
				if (from < scrollY && to > scrollY) {
					!($this.css("position") == "fixed") && $this.css({
						left: $this.offset().left,
						top: data.pad.top
					}).css("position", "fixed");
					if (options.activeClass) { $this.addClass(options.activeClass); }
				} else if (scrollY >= to) {
					$this.css({
						left: "",
						top: to - data.parentTop + data.pad.top
					}).css("position", "absolute");
					if (options.activeClass) { $this.addClass(options.activeClass); }
				} else {
					$this.css({position: "", top: "", left: ""});
					if (options.activeClass) { $this.removeClass(options.activeClass); }
				}
		  }
		  elements = elmts;
		};

		var update = function () { recalculateLimits(); onScroll(); };

		this.each(function () {
			var $this = $(this), 
				data  = $(this).data('pin') || {};

			if (data && data.update) { return; }
			elements.push($this);
			$("img", this).one("load", recalculateLimits);
			data.update = update;
			$(this).data('pin', data);
		});

		$window.scroll(onScroll);
		$window.resize(function () { recalculateLimits(); });
		recalculateLimits();

		$window.on('load', update);

		return this;
	  };
})(jQuery);


( function( $ ) {
	"use strict";
	
	// Define default settings
	var defaults = {
		action: function() {},
		runOnLoad: false,
		duration: 500
	};
	
	// Define global variables
	var settings = defaults,
		running = false,
		start;
	
	var methods = {};
	
	// Initial plugin configuration
	methods.init = function() {
		
		// Allocate passed arguments to settings based on type
		for( var i = 0; i <= arguments.length; i++ ) {
			var arg = arguments[i];
			switch ( typeof arg ) {
				case "function":
					settings.action = arg;
					break;
				case "boolean":
					settings.runOnLoad = arg;
					break;
				case "number":
					settings.duration = arg;
					break;
			}
		}
	
		// Process each matching jQuery object
		return this.each(function() {
		
			if( settings.runOnLoad ) { settings.action(); }
			
			$(this).resize( function() {
				
				methods.timedAction.call( this );
				
			} );
		
		} );
	};
	
	methods.timedAction = function( code, millisec ) {
		
		var doAction = function() {
			var remaining = settings.duration;
			
			if( running ) {
				var elapse = new Date() - start;
				remaining = settings.duration - elapse;
				if( remaining <= 0 ) {
					// Clear timeout and reset running variable
					clearTimeout(running);
					running = false;
					// Perform user defined function
					settings.action();
				
					return;
				}
			}
			wait( remaining );
		};
		
		var wait = function( time ) {
			running = setTimeout( doAction, time );
		};
		
		// Define new action starting time
		start = new Date();
		
		// Define runtime settings if function is run directly
		if( typeof millisec === 'number' ) { settings.duration = millisec; }
		if( typeof code === 'function' ) { settings.action = code; }
		
		// Only run timed loop if not already running
		if( !running ) { doAction(); }
		
	};

	
	$.fn.afterResize = function( method ) {
		
		if( methods[method] ) {
			return methods[method].apply( this, Array.prototype.slice.call( arguments, 1 ) );
		} else {
			return methods.init.apply( this, arguments );
		}
		
	};
	
})(jQuery);


/*
Plugin Name: 	Animated Headlines
Written by: 	Codyhouse - (https://codyhouse.co/demo/animated-headlines/index.html)
*/
jQuery(document).ready(function($) {
	//set animation timing
	var animationDelay = 2500,
		//loading bar effect
		barAnimationDelay = 3800,
		barWaiting = barAnimationDelay - 3000, //3000 is the duration of the transition on the loading bar - set in the scss/css file
		//letters effect
		lettersDelay = 50,
		//type effect
		typeLettersDelay = 150,
		selectionDuration = 500,
		typeAnimationDelay = selectionDuration + 800,
		//clip effect 
		revealDuration = 600,
		revealAnimationDelay = 1500;

	initHeadline();

	function initHeadline() {
		//initialise headline animation
		animateHeadline('.word-rotator', '.word-rotator.letters');
	}

	function animateHeadline($selector) {
		var duration = animationDelay;

		theme.fn.intObs($selector, function(){
			
			// Single Letters - Insert <i> element for each letter of a changing word
			if( $(this).hasClass('letters') ) {
				$(this).find('b').each(function() {
					var word = $(this),
						letters = word.text().split(''),
						selected = word.hasClass('is-visible');
					for (i in letters) {
						if (word.parents('.rotate-2').length > 0) letters[i] = '<em>' + letters[i] + '</em>';
						letters[i] = (selected) ? '<i class="in">' + letters[i] + '</i>' : '<i>' + letters[i] + '</i>';
					}
					var newLetters = letters.join('');
					word.html(newLetters).css('opacity', 1);
				});				
			}

			// Animate the Headline
			var headline = $(this);

			if (headline.hasClass('loading-bar')) {
				duration = barAnimationDelay;
				setTimeout(function() {
					headline.find('.word-rotator-words').addClass('is-loading')
				}, barWaiting);
			} else if (headline.hasClass('clip')) {
				var spanWrapper = headline.find('.word-rotator-words'),
					newWidth = spanWrapper.outerWidth() + 10
				spanWrapper.css('width', newWidth);
			} else if (!headline.hasClass('type')) {
				//assign to .word-rotator-words the width of its longest word
				var words = headline.find('.word-rotator-words b'),
					width = 0;
				words.each(function() {
					var wordWidth = $(this).outerWidth();
					if (wordWidth > width) width = wordWidth;
				});
				headline.find('.word-rotator-words').css('width', width);
			};

			// Trigger animation
			setTimeout(function() {
				hideWord(headline.find('.is-visible').eq(0))
			}, duration);
		}, {});
	}

	function hideWord($word) {
		var nextWord = takeNext($word);

		if ($word.parents('.word-rotator').hasClass('type')) {
			var parentSpan = $word.parent('.word-rotator-words');
			parentSpan.addClass('selected').removeClass('waiting');
			setTimeout(function() {
				parentSpan.removeClass('selected');
				$word.removeClass('is-visible').addClass('is-hidden').children('i').removeClass('in').addClass('out');
			}, selectionDuration);
			setTimeout(function() {
				showWord(nextWord, typeLettersDelay)
			}, typeAnimationDelay);

		} else if ($word.parents('.word-rotator').hasClass('letters')) {
			var bool = ($word.children('i').length >= nextWord.children('i').length) ? true : false;
			hideLetter($word.find('i').eq(0), $word, bool, lettersDelay);
			showLetter(nextWord.find('i').eq(0), nextWord, bool, lettersDelay);

		} else if ($word.parents('.word-rotator').hasClass('clip')) {
			$word.parents('.word-rotator-words').stop( true, true ).animate({
				width: '2px'
			}, revealDuration, function() {
				switchWord($word, nextWord);
				showWord(nextWord);
			});

		} else if ($word.parents('.word-rotator').hasClass('loading-bar')) {
			$word.parents('.word-rotator-words').removeClass('is-loading');
			switchWord($word, nextWord);
			setTimeout(function() {
				hideWord(nextWord)
			}, barAnimationDelay);
			setTimeout(function() {
				$word.parents('.word-rotator-words').addClass('is-loading')
			}, barWaiting);

		} else {
			switchWord($word, nextWord);
			setTimeout(function() {
				hideWord(nextWord)
			}, animationDelay);
		}
	}

	function showWord($word, $duration) {
		if ($word.parents('.word-rotator').hasClass('type')) {
			showLetter($word.find('i').eq(0), $word, false, $duration);
			$word.addClass('is-visible').removeClass('is-hidden');
		} else if ($word.parents('.word-rotator').hasClass('clip')) {
			if (document.hasFocus()) {
				$word.parents('.word-rotator-words').stop( true, true ).animate({
					'width': $word.outerWidth() + 10
				}, revealDuration, function() {
					setTimeout(function() {
						hideWord($word)
					}, revealAnimationDelay);
				});
			} else {
				$word.parents('.word-rotator-words').stop( true, true ).animate({
					width: $word.outerWidth() + 10
				});
				setTimeout(function() {
					hideWord($word)
				}, revealAnimationDelay);
			}
		}
	}

	function hideLetter($letter, $word, $bool, $duration) {
		$letter.removeClass('in').addClass('out');

		if (!$letter.is(':last-child')) {
			setTimeout(function() {
				hideLetter($letter.next(), $word, $bool, $duration);
			}, $duration);
		} else if ($bool) {
			setTimeout(function() {
				hideWord(takeNext($word))
			}, animationDelay);
		}

		if ($letter.is(':last-child') && $('html').hasClass('no-csstransitions')) {
			var nextWord = takeNext($word);
			switchWord($word, nextWord);
		}
	}

	function showLetter($letter, $word, $bool, $duration) {
		$letter.addClass('in').removeClass('out');

		if (!$letter.is(':last-child')) {
			setTimeout(function() {
				showLetter($letter.next(), $word, $bool, $duration);
			}, $duration);
		} else {
			if ($word.parents('.word-rotator').hasClass('type')) {
				setTimeout(function() {
					$word.parents('.word-rotator-words').addClass('waiting');
				}, 200);
			}
			if (!$bool) {
				setTimeout(function() {
					hideWord($word)
				}, animationDelay)
			}

			if (!$word.closest('.word-rotator').hasClass('type')) {
				$word.closest('.word-rotator-words').stop( true, true ).animate({
					width: $word.outerWidth()
				});
			}
		}
	}

	function takeNext($word) {
		return (!$word.is(':last-child')) ? $word.next() : $word.parent().children().eq(0);
	}

	function takePrev($word) {
		return (!$word.is(':first-child')) ? $word.prev() : $word.parent().children().last();
	}

	function switchWord($oldWord, $newWord) {
		$oldWord.removeClass('is-visible').addClass('is-hidden');
		$newWord.removeClass('is-hidden').addClass('is-visible');

		if (!$newWord.closest('.word-rotator').hasClass('clip')) {
			var space = 0,
				delay = ($newWord.outerWidth() > $oldWord.outerWidth()) ? 0 : 600;

			if ($newWord.closest('.word-rotator').hasClass('loading-bar') || $newWord.closest('.word-rotator').hasClass('slide')) {
				space = 3;
				delay = 0;
			}

			setTimeout(function() {
				$newWord.closest('.word-rotator-words').stop( true, true ).animate({
					width: $newWord.outerWidth() + space
				});
			}, delay);
		}
	}
});

/*
jQuery Hover3d
=================================================
Version: 1.1.0
Author: Rian Ariona
Website: http://ariona.net
Docs: http://ariona.github.io/hover3d
Repo: http://github.com/ariona/hover3d
Issues: http://github.com/ariona/hover3d/issues
*/

(function($){
	
	$.fn.hover3d = function(options){
		
		var settings = $.extend({
			selector      : null,
			perspective   : 1000,
			sensitivity   : 20,
			invert        : false,
			shine         : false,
			hoverInClass  : "hover-in",
			hoverOutClass : "hover-out",
			hoverClass    : "hover-3d"
		}, options);
		
		return this.each(function(){
			
			var $this = $(this),
				$card = $this.find(settings.selector);
				currentX = 0;
				currentY = 0;


			if( settings.shine ){
				$card.append('<div class="shine"></div>');
			}
			var $shine = $(this).find(".shine");

			// Set perspective and transformStyle value
			// for element and 3d object
			$this.css({
				perspective: settings.perspective+"px",
				transformStyle: "preserve-3d"
			});
			
			$card.css({
				perspective: settings.perspective+"px",
				transformStyle: "preserve-3d",
			});

			$shine.css({
				position  : "absolute",
				top       : 0,
				left      : 0,
				bottom    : 0,
				right     : 0,
				transform : 'translateZ(1px)',
				"z-index" : 9
			});
			
			// Mouse Enter function, this will add hover-in
			// Class so when mouse over it will add transition
			// based on hover-in class
			function enter(event){
				$card.addClass(settings.hoverInClass+" "+settings.hoverClass);
				currentX = currentY = 0;
				setTimeout(function(){
					$card.removeClass(settings.hoverInClass);
				}, 1000);
			}
			
			// Mouse movement Parallax effect
			function move(event){
				
				var w      = $card.innerWidth(),
					h      = $card.innerHeight(),
					currentX = Math.round(event.pageX - $card.offset().left),
					currentY = Math.round(event.pageY - $card.offset().top),
					ax 	   = settings.invert ?  ( w / 2 - currentX)/settings.sensitivity : -( w / 2 - currentX)/settings.sensitivity,
					ay     = settings.invert ? -( h / 2 - currentY)/settings.sensitivity :  ( h / 2 - currentY)/settings.sensitivity,
					dx     = currentX - w / 2,
					dy     = currentY - h / 2,
					theta  = Math.atan2(dy, dx),
					angle  = theta * 180 / Math.PI - 90;

					
				if (angle < 0) {
					angle  = angle + 360;
				}
				

				$card.css({
					perspective    : settings.perspective+"px",
					transformStyle : "preserve-3d",
					transform      : "rotateY("+ax+"deg) rotateX("+ay+"deg)"
				});

				$shine.css('background', 'linear-gradient(' + angle + 'deg, rgba(255,255,255,' + event.offsetY / h * .5 + ') 0%,rgba(255,255,255,0) 80%)');
			}
			
			// Mouse leave function, will set the transform
			// property to 0, and add transition class
			// for exit animation
			function leave(){
				$card.addClass(settings.hoverOutClass+" "+settings.hoverClass);
				$card.css({
					perspective    : settings.perspective+"px",
					transformStyle : "preserve-3d",
					transform      : "rotateX(0) rotateY(0)"
				});
				setTimeout( function(){
					$card.removeClass(settings.hoverOutClass+" "+settings.hoverClass);
					currentX = currentY = 0;
				}, 1000 );
			}
			
			// Mouseenter event binding
			$this.on( "mouseenter", function(){
				return enter();
			});
			
			// Mousemove event binding
			$this.on( "mousemove", function(event){
				return move(event);
			});
			
			// Mouseleave event binding
			$this.on( "mouseleave", function(){
				return leave();
			});
			
		});
		
	};
	
}(jQuery));


/**
 * jquery.hoverdir.js v1.1.0
 * http://www.codrops.com
 *
 * Licensed under the MIT license.
 * http://www.opensource.org/licenses/mit-license.php
 * 
 * Copyright 2012, Codrops
 * http://www.codrops.com
 */
;( function( $, window, undefined ) {
	
	'use strict';

	$.HoverDir = function( options, element ) {
		
		this.$el = $( element );
		this._init( options );

	};

	// the options
	$.HoverDir.defaults = {
		speed : 300,
		easing : 'ease',
		hoverDelay : 0,
		inverse : false,
		hoverElem: '.thumb-info-wrapper-overlay'
	};

	$.HoverDir.prototype = {

		_init : function( options ) {
			
			// options
			this.options = $.extend( true, {}, $.HoverDir.defaults, options );
			// transition properties
			this.transitionProp = 'all ' + this.options.speed + 'ms ' + this.options.easing;
			// support for CSS transitions
			this.support = Modernizr.csstransitions;
			// load the events
			this._loadEvents();

		},
		_loadEvents : function() {

			var self = this;
			
			this.$el.on( 'mouseenter.hoverdir, mouseleave.hoverdir', function( event ) {
				
				var $el = $( this ),
					$hoverElem = $el.find( self.options.hoverElem ),
					direction = self._getDir( $el, { x : event.pageX, y : event.pageY } ),
					styleCSS = self._getStyle( direction );
				
				if( event.type === 'mouseenter' ) {
					
					$hoverElem.hide().css( styleCSS.from );
					clearTimeout( self.tmhover );

					self.tmhover = setTimeout( function() {
						
						$hoverElem.show( 0, function() {
							
							var $el = $( this );
							if( self.support ) {
								$el.css( 'transition', self.transitionProp );
							}
							self._applyAnimation( $el, styleCSS.to, self.options.speed );

						} );
						
					
					}, self.options.hoverDelay );
					
				}
				else {
				
					if( self.support ) {
						$hoverElem.css( 'transition', self.transitionProp );
					}
					clearTimeout( self.tmhover );
					self._applyAnimation( $hoverElem, styleCSS.from, self.options.speed );
					
				}
					
			} );

		},
		// credits : http://stackoverflow.com/a/3647634
		_getDir : function( $el, coordinates ) {
			
			// the width and height of the current div
			var w = $el.width(),
				h = $el.height(),

				// calculate the x and y to get an angle to the center of the div from that x and y.
				// gets the x value relative to the center of the DIV and "normalize" it
				x = ( coordinates.x - $el.offset().left - ( w/2 )) * ( w > h ? ( h/w ) : 1 ),
				y = ( coordinates.y - $el.offset().top  - ( h/2 )) * ( h > w ? ( w/h ) : 1 ),
			
				// the angle and the direction from where the mouse came in/went out clockwise (TRBL=0123);
				// first calculate the angle of the point,
				// add 180 deg to get rid of the negative values
				// divide by 90 to get the quadrant
				// add 3 and do a modulo by 4  to shift the quadrants to a proper clockwise TRBL (top/right/bottom/left) **/
				direction = Math.round( ( ( ( Math.atan2(y, x) * (180 / Math.PI) ) + 180 ) / 90 ) + 3 ) % 4;
			
			return direction;
			
		},
		_getStyle : function( direction ) {
			
			var fromStyle, toStyle,
				slideFromTop = { left : '0px', top : '-100%' },
				slideFromBottom = { left : '0px', top : '100%' },
				slideFromLeft = { left : '-100%', top : '0px' },
				slideFromRight = { left : '100%', top : '0px' },
				slideTop = { top : '0px' },
				slideLeft = { left : '0px' };
			
			switch( direction ) {
				case 0:
					// from top
					fromStyle = !this.options.inverse ? slideFromTop : slideFromBottom;
					toStyle = slideTop;
					break;
				case 1:
					// from right
					fromStyle = !this.options.inverse ? slideFromRight : slideFromLeft;
					toStyle = slideLeft;
					break;
				case 2:
					// from bottom
					fromStyle = !this.options.inverse ? slideFromBottom : slideFromTop;
					toStyle = slideTop;
					break;
				case 3:
					// from left
					fromStyle = !this.options.inverse ? slideFromLeft : slideFromRight;
					toStyle = slideLeft;
					break;
			};
			
			return { from : fromStyle, to : toStyle };
					
		},
		// apply a transition or fallback to jquery animate based on Modernizr.csstransitions support
		_applyAnimation : function( el, styleCSS, speed ) {

			$.fn.applyStyle = this.support ? $.fn.css : $.fn.animate;
			el.stop().applyStyle( styleCSS, $.extend( true, [], { duration : speed + 'ms' } ) );

		},

	};
	
	var logError = function( message ) {

		if ( window.console ) {

			window.console.error( message );
		
		}

	};
	
	$.fn.hoverdir = function( options ) {

		var instance = $.data( this, 'hoverdir' );
		
		if ( typeof options === 'string' ) {
			
			var args = Array.prototype.slice.call( arguments, 1 );
			
			this.each(function() {
			
				if ( !instance ) {

					logError( "cannot call methods on hoverdir prior to initialization; " +
					"attempted to call method '" + options + "'" );
					return;
				
				}
				
				if ( !$.isFunction( instance[options] ) || options.charAt(0) === "_" ) {

					logError( "no such method '" + options + "' for hoverdir instance" );
					return;
				
				}
				
				instance[ options ].apply( instance, args );
			
			});
		
		} 
		else {
		
			this.each(function() {
				
				if ( instance ) {

					instance._init();
				
				}
				else {

					instance = $.data( this, 'hoverdir', new $.HoverDir( options, this ) );
				
				}

			});
		
		}
		
		return instance;
		
	};
	
} )( jQuery, window );


/*!
 * GSAP 3.9.1
 * https://greensock.com
 * 
 * @license Copyright 2021, GreenSock. All rights reserved.
 * Subject to the terms at https://greensock.com/standard-license or for Club GreenSock members, the agreement issued with that membership.
 * @author: Jack Doyle, jack@greensock.com
 */

!function(t,e){"object"==typeof exports&&"undefined"!=typeof module?e(exports):"function"==typeof define&&define.amd?define(["exports"],e):e((t=t||self).window=t.window||{})}(this,function(e){"use strict";function _inheritsLoose(t,e){t.prototype=Object.create(e.prototype),(t.prototype.constructor=t).__proto__=e}function _assertThisInitialized(t){if(void 0===t)throw new ReferenceError("this hasn't been initialised - super() hasn't been called");return t}function o(t){return"string"==typeof t}function p(t){return"function"==typeof t}function q(t){return"number"==typeof t}function r(t){return void 0===t}function s(t){return"object"==typeof t}function t(t){return!1!==t}function u(){return"undefined"!=typeof window}function v(t){return p(t)||o(t)}function M(t){return(h=mt(t,ot))&&oe}function N(t,e){return console.warn("Invalid property",t,"set to",e,"Missing plugin? gsap.registerPlugin()")}function O(t,e){return!e&&console.warn(t)}function P(t,e){return t&&(ot[t]=e)&&h&&(h[t]=e)||ot}function Q(){return 0}function $(t){var e,r,i=t[0];if(s(i)||p(i)||(t=[t]),!(e=(i._gsap||{}).harness)){for(r=ct.length;r--&&!ct[r].targetTest(i););e=ct[r]}for(r=t.length;r--;)t[r]&&(t[r]._gsap||(t[r]._gsap=new Lt(t[r],e)))||t.splice(r,1);return t}function _(t){return t._gsap||$(xt(t))[0]._gsap}function aa(t,e,i){return(i=t[e])&&p(i)?t[e]():r(i)&&t.getAttribute&&t.getAttribute(e)||i}function ba(t,e){return(t=t.split(",")).forEach(e)||t}function ca(t){return Math.round(1e5*t)/1e5||0}function da(t){return Math.round(1e7*t)/1e7||0}function ea(t,e){for(var r=e.length,i=0;t.indexOf(e[i])<0&&++i<r;);return i<r}function fa(){var t,e,r=ht.length,i=ht.slice(0);for(lt={},t=ht.length=0;t<r;t++)(e=i[t])&&e._lazy&&(e.render(e._lazy[0],e._lazy[1],!0)._lazy=0)}function ga(t,e,r,i){ht.length&&fa(),t.render(e,r,i),ht.length&&fa()}function ha(t){var e=parseFloat(t);return(e||0===e)&&(t+"").match(at).length<2?e:o(t)?t.trim():t}function ia(t){return t}function ja(t,e){for(var r in e)r in t||(t[r]=e[r]);return t}function ma(t,e){for(var r in e)"__proto__"!==r&&"constructor"!==r&&"prototype"!==r&&(t[r]=s(e[r])?ma(t[r]||(t[r]={}),e[r]):e[r]);return t}function na(t,e){var r,i={};for(r in t)r in e||(i[r]=t[r]);return i}function oa(e){var r=e.parent||I,i=e.keyframes?function _setKeyframeDefaults(i){return function(t,e){for(var r in e)r in t||"duration"===r&&i||"ease"===r||(t[r]=e[r])}}(W(e.keyframes)):ja;if(t(e.inherit))for(;r;)i(e,r.vars.defaults),r=r.parent||r._dp;return e}function ra(t,e,r,i){void 0===r&&(r="_first"),void 0===i&&(i="_last");var n=e._prev,a=e._next;n?n._next=a:t[r]===e&&(t[r]=a),a?a._prev=n:t[i]===e&&(t[i]=n),e._next=e._prev=e.parent=null}function sa(t,e){!t.parent||e&&!t.parent.autoRemoveChildren||t.parent.remove(t),t._act=0}function ta(t,e){if(t&&(!e||e._end>t._dur||e._start<0))for(var r=t;r;)r._dirty=1,r=r.parent;return t}function wa(t){return t._repeat?gt(t._tTime,t=t.duration()+t._rDelay)*t:0}function ya(t,e){return(t-e._start)*e._ts+(0<=e._ts?0:e._dirty?e.totalDuration():e._tDur)}function za(t){return t._end=da(t._start+(t._tDur/Math.abs(t._ts||t._rts||X)||0))}function Aa(t,e){var r=t._dp;return r&&r.smoothChildTiming&&t._ts&&(t._start=da(r._time-(0<t._ts?e/t._ts:((t._dirty?t.totalDuration():t._tDur)-e)/-t._ts)),za(t),r._dirty||ta(r,t)),t}function Ba(t,e){var r;if((e._time||e._initted&&!e._dur)&&(r=ya(t.rawTime(),e),(!e._dur||Tt(0,e.totalDuration(),r)-e._tTime>X)&&e.render(r,!0)),ta(t,e)._dp&&t._initted&&t._time>=t._dur&&t._ts){if(t._dur<t.duration())for(r=t;r._dp;)0<=r.rawTime()&&r.totalTime(r._tTime),r=r._dp;t._zTime=-X}}function Ca(t,e,r,i){return e.parent&&sa(e),e._start=da((q(r)?r:r||t!==I?bt(t,r,e):t._time)+e._delay),e._end=da(e._start+(e.totalDuration()/Math.abs(e.timeScale())||0)),function _addLinkedListItem(t,e,r,i,n){void 0===r&&(r="_first"),void 0===i&&(i="_last");var a,s=t[i];if(n)for(a=e[n];s&&s[n]>a;)s=s._prev;s?(e._next=s._next,s._next=e):(e._next=t[r],t[r]=e),e._next?e._next._prev=e:t[i]=e,e._prev=s,e.parent=e._dp=t}(t,e,"_first","_last",t._sort?"_start":0),vt(e)||(t._recent=e),i||Ba(t,e),t}function Da(t,e){return(ot.ScrollTrigger||N("scrollTrigger",e))&&ot.ScrollTrigger.create(e,t)}function Ea(t,e,r,i){return jt(t,e),t._initted?!r&&t._pt&&(t._dur&&!1!==t.vars.lazy||!t._dur&&t.vars.lazy)&&f!==St.frame?(ht.push(t),t._lazy=[e,i],1):void 0:1}function Ja(t,e,r,i){var n=t._repeat,a=da(e)||0,s=t._tTime/t._tDur;return s&&!i&&(t._time*=a/t._dur),t._dur=a,t._tDur=n?n<0?1e10:da(a*(n+1)+t._rDelay*n):a,0<s&&!i?Aa(t,t._tTime=t._tDur*s):t.parent&&za(t),r||ta(t.parent,t),t}function Ka(t){return t instanceof Nt?ta(t):Ja(t,t._dur)}function Na(e,r,i){var n,a,s=q(r[1]),o=(s?2:1)+(e<2?0:1),u=r[o];if(s&&(u.duration=r[1]),u.parent=i,e){for(n=u,a=i;a&&!("immediateRender"in n);)n=a.vars.defaults||{},a=t(a.vars.inherit)&&a.parent;u.immediateRender=t(n.immediateRender),e<2?u.runBackwards=1:u.startAt=r[o-1]}return new Jt(r[0],u,r[1+o])}function Oa(t,e){return t||0===t?e(t):e}function Qa(t,e){return o(t)&&(e=st.exec(t))?t.substr(e.index+e[0].length):""}function Ta(t,e){return t&&s(t)&&"length"in t&&(!e&&!t.length||t.length-1 in t&&s(t[0]))&&!t.nodeType&&t!==i}function Xa(t){return t.sort(function(){return.5-Math.random()})}function Ya(t){if(p(t))return t;var c=s(t)?t:{each:t},_=Rt(c.ease),m=c.from||0,g=parseFloat(c.base)||0,v={},e=0<m&&m<1,y=isNaN(m)||e,b=c.axis,T=m,w=m;return o(m)?T=w={center:.5,edges:.5,end:1}[m]||0:!e&&y&&(T=m[0],w=m[1]),function(t,e,r){var i,n,a,s,o,u,h,l,f,d=(r||c).length,p=v[d];if(!p){if(!(f="auto"===c.grid?0:(c.grid||[1,j])[1])){for(h=-j;h<(h=r[f++].getBoundingClientRect().left)&&f<d;);f--}for(p=v[d]=[],i=y?Math.min(f,d)*T-.5:m%f,n=f===j?0:y?d*w/f-.5:m/f|0,l=j,u=h=0;u<d;u++)a=u%f-i,s=n-(u/f|0),p[u]=o=b?Math.abs("y"===b?s:a):G(a*a+s*s),h<o&&(h=o),o<l&&(l=o);"random"===m&&Xa(p),p.max=h-l,p.min=l,p.v=d=(parseFloat(c.amount)||parseFloat(c.each)*(d<f?d-1:b?"y"===b?d/f:f:Math.max(f,d/f))||0)*("edges"===m?-1:1),p.b=d<0?g-d:g,p.u=Qa(c.amount||c.each)||0,_=_&&d<0?Bt(_):_}return d=(p[t]-p.min)/p.max||0,da(p.b+(_?_(d):d)*p.v)+p.u}}function Za(r){var i=Math.pow(10,((r+"").split(".")[1]||"").length);return function(t){var e=Math.round(parseFloat(t)/r)*r*i;return(e-e%1)/i+(q(t)?0:Qa(t))}}function $a(u,t){var h,l,e=W(u);return!e&&s(u)&&(h=e=u.radius||j,u.values?(u=xt(u.values),(l=!q(u[0]))&&(h*=h)):u=Za(u.increment)),Oa(t,e?p(u)?function(t){return l=u(t),Math.abs(l-t)<=h?l:t}:function(t){for(var e,r,i=parseFloat(l?t.x:t),n=parseFloat(l?t.y:0),a=j,s=0,o=u.length;o--;)(e=l?(e=u[o].x-i)*e+(r=u[o].y-n)*r:Math.abs(u[o]-i))<a&&(a=e,s=o);return s=!h||a<=h?u[s]:t,l||s===t||q(t)?s:s+Qa(t)}:Za(u))}function _a(t,e,r,i){return Oa(W(t)?!e:!0===r?!!(r=0):!i,function(){return W(t)?t[~~(Math.random()*t.length)]:(r=r||1e-5)&&(i=r<1?Math.pow(10,(r+"").length-2):1)&&Math.floor(Math.round((t-r/2+Math.random()*(e-t+.99*r))/r)*r*i)/i})}function db(e,r,t){return Oa(t,function(t){return e[~~r(t)]})}function gb(t){for(var e,r,i,n,a=0,s="";~(e=t.indexOf("random(",a));)i=t.indexOf(")",e),n="["===t.charAt(e+7),r=t.substr(e+7,i-e-7).match(n?at:tt),s+=t.substr(a,e-a)+_a(n?r:+r[0],n?0:+r[1],+r[2]||1e-5),a=i+1;return s+t.substr(a,t.length-a)}function jb(t,e,r){var i,n,a,s=t.labels,o=j;for(i in s)(n=s[i]-e)<0==!!r&&n&&o>(n=Math.abs(n))&&(a=i,o=n);return a}function lb(t){return sa(t),t.scrollTrigger&&t.scrollTrigger.kill(!1),t.progress()<1&&Mt(t,"onInterrupt"),t}function qb(t,e,r){return(6*(t+=t<0?1:1<t?-1:0)<1?e+(r-e)*t*6:t<.5?r:3*t<2?e+(r-e)*(2/3-t)*6:e)*kt+.5|0}function rb(t,e,r){var i,n,a,s,o,u,h,l,f,d,p=t?q(t)?[t>>16,t>>8&kt,t&kt]:0:Ct.black;if(!p){if(","===t.substr(-1)&&(t=t.substr(0,t.length-1)),Ct[t])p=Ct[t];else if("#"===t.charAt(0)){if(t.length<6&&(t="#"+(i=t.charAt(1))+i+(n=t.charAt(2))+n+(a=t.charAt(3))+a+(5===t.length?t.charAt(4)+t.charAt(4):"")),9===t.length)return[(p=parseInt(t.substr(1,6),16))>>16,p>>8&kt,p&kt,parseInt(t.substr(7),16)/255];p=[(t=parseInt(t.substr(1),16))>>16,t>>8&kt,t&kt]}else if("hsl"===t.substr(0,3))if(p=d=t.match(tt),e){if(~t.indexOf("="))return p=t.match(et),r&&p.length<4&&(p[3]=1),p}else s=+p[0]%360/360,o=p[1]/100,i=2*(u=p[2]/100)-(n=u<=.5?u*(o+1):u+o-u*o),3<p.length&&(p[3]*=1),p[0]=qb(s+1/3,i,n),p[1]=qb(s,i,n),p[2]=qb(s-1/3,i,n);else p=t.match(tt)||Ct.transparent;p=p.map(Number)}return e&&!d&&(i=p[0]/kt,n=p[1]/kt,a=p[2]/kt,u=((h=Math.max(i,n,a))+(l=Math.min(i,n,a)))/2,h===l?s=o=0:(f=h-l,o=.5<u?f/(2-h-l):f/(h+l),s=h===i?(n-a)/f+(n<a?6:0):h===n?(a-i)/f+2:(i-n)/f+4,s*=60),p[0]=~~(s+.5),p[1]=~~(100*o+.5),p[2]=~~(100*u+.5)),r&&p.length<4&&(p[3]=1),p}function sb(t){var r=[],i=[],n=-1;return t.split(Pt).forEach(function(t){var e=t.match(rt)||[];r.push.apply(r,e),i.push(n+=e.length+1)}),r.c=i,r}function tb(t,e,r){var i,n,a,s,o="",u=(t+o).match(Pt),h=e?"hsla(":"rgba(",l=0;if(!u)return t;if(u=u.map(function(t){return(t=rb(t,e,1))&&h+(e?t[0]+","+t[1]+"%,"+t[2]+"%,"+t[3]:t.join(","))+")"}),r&&(a=sb(t),(i=r.c).join(o)!==a.c.join(o)))for(s=(n=t.replace(Pt,"1").split(rt)).length-1;l<s;l++)o+=n[l]+(~i.indexOf(l)?u.shift()||h+"0,0,0,0)":(a.length?a:u.length?u:r).shift());if(!n)for(s=(n=t.split(Pt)).length-1;l<s;l++)o+=n[l]+u[l];return o+n[s]}function wb(t){var e,r=t.join(" ");if(Pt.lastIndex=0,Pt.test(r))return e=At.test(r),t[1]=tb(t[1],e),t[0]=tb(t[0],e,sb(t[1])),!0}function Fb(t){var e=(t+"").split("("),r=zt[e[0]];return r&&1<e.length&&r.config?r.config.apply(null,~t.indexOf("{")?[function _parseObjectInString(t){for(var e,r,i,n={},a=t.substr(1,t.length-3).split(":"),s=a[0],o=1,u=a.length;o<u;o++)r=a[o],e=o!==u-1?r.lastIndexOf(","):r.length,i=r.substr(0,e),n[s]=isNaN(i)?i.replace(Et,"").trim():+i,s=r.substr(e+1).trim();return n}(e[1])]:function _valueInParentheses(t){var e=t.indexOf("(")+1,r=t.indexOf(")"),i=t.indexOf("(",e);return t.substring(e,~i&&i<r?t.indexOf(")",r+1):r)}(t).split(",").map(ha)):zt._CE&&Ft.test(t)?zt._CE("",t):r}function Hb(t,e){for(var r,i=t._first;i;)i instanceof Nt?Hb(i,e):!i.vars.yoyoEase||i._yoyo&&i._repeat||i._yoyo===e||(i.timeline?Hb(i.timeline,e):(r=i._ease,i._ease=i._yEase,i._yEase=r,i._yoyo=e)),i=i._next}function Jb(t,e,r,i){void 0===r&&(r=function easeOut(t){return 1-e(1-t)}),void 0===i&&(i=function easeInOut(t){return t<.5?e(2*t)/2:1-e(2*(1-t))/2});var n,a={easeIn:e,easeOut:r,easeInOut:i};return ba(t,function(t){for(var e in zt[t]=ot[t]=a,zt[n=t.toLowerCase()]=r,a)zt[n+("easeIn"===e?".in":"easeOut"===e?".out":".inOut")]=zt[t+"."+e]=a[e]}),a}function Kb(e){return function(t){return t<.5?(1-e(1-2*t))/2:.5+e(2*(t-.5))/2}}function Lb(r,t,e){function Sl(t){return 1===t?1:i*Math.pow(2,-10*t)*K((t-a)*n)+1}var i=1<=t?t:1,n=(e||(r?.3:.45))/(t<1?t:1),a=n/U*(Math.asin(1/i)||0),s="out"===r?Sl:"in"===r?function(t){return 1-Sl(1-t)}:Kb(Sl);return n=U/n,s.config=function(t,e){return Lb(r,t,e)},s}function Mb(e,r){function $l(t){return t?--t*t*((r+1)*t+r)+1:0}void 0===r&&(r=1.70158);var t="out"===e?$l:"in"===e?function(t){return 1-$l(1-t)}:Kb($l);return t.config=function(t){return Mb(e,t)},t}var R,I,i,n,a,h,l,f,d,c,m,g,y,b,T,w,x,k,C,A,S,D,z,F,E,B,Y={autoSleep:120,force3D:"auto",nullTargetWarn:1,units:{lineHeight:""}},L={duration:.5,overwrite:!1,delay:0},j=1e8,X=1/j,U=2*Math.PI,V=U/4,J=0,G=Math.sqrt,Z=Math.cos,K=Math.sin,H="function"==typeof ArrayBuffer&&ArrayBuffer.isView||function(){},W=Array.isArray,tt=/(?:-?\.?\d|\.)+/gi,et=/[-+=.]*\d+[.e\-+]*\d*[e\-+]*\d*/g,rt=/[-+=.]*\d+[.e-]*\d*[a-z%]*/g,it=/[-+=.]*\d+\.?\d*(?:e-|e\+)?\d*/gi,nt=/[+-]=-?[.\d]+/,at=/[^,'"\[\]\s]+/gi,st=/[\d.+\-=]+(?:e[-+]\d*)*/i,ot={},ut={},ht=[],lt={},ft={},dt={},pt=30,ct=[],_t="",mt=function _merge(t,e){for(var r in e)t[r]=e[r];return t},gt=function _animationCycle(t,e){var r=Math.floor(t/=e);return t&&r===t?r-1:r},vt=function _isFromOrFromStart(t){var e=t.data;return"isFromStart"===e||"isStart"===e},yt={_start:0,endTime:Q,totalDuration:Q},bt=function _parsePosition(t,e,r){var i,n,a,s=t.labels,u=t._recent||yt,h=t.duration()>=j?u.endTime(!1):t._dur;return o(e)&&(isNaN(e)||e in s)?(n=e.charAt(0),a="%"===e.substr(-1),i=e.indexOf("="),"<"===n||">"===n?(0<=i&&(e=e.replace(/=/,"")),("<"===n?u._start:u.endTime(0<=u._repeat))+(parseFloat(e.substr(1))||0)*(a?(i<0?u:r).totalDuration()/100:1)):i<0?(e in s||(s[e]=h),s[e]):(n=parseFloat(e.charAt(i-1)+e.substr(i+1)),a&&r&&(n=n/100*(W(r)?r[0]:r).totalDuration()),1<i?_parsePosition(t,e.substr(0,i-1),r)+n:h+n)):null==e?h:+e},Tt=function _clamp(t,e,r){return r<t?t:e<r?e:r},wt=[].slice,xt=function toArray(t,e,r){return!o(t)||r||!n&&Dt()?W(t)?function _flatten(t,e,r){return void 0===r&&(r=[]),t.forEach(function(t){return o(t)&&!e||Ta(t,1)?r.push.apply(r,xt(t)):r.push(t)})||r}(t,r):Ta(t)?wt.call(t,0):t?[t]:[]:wt.call((e||a).querySelectorAll(t),0)},Ot=function mapRange(e,t,r,i,n){var a=t-e,s=i-r;return Oa(n,function(t){return r+((t-e)/a*s||0)})},Mt=function _callback(t,e,r){var i,n,a=t.vars,s=a[e];if(s)return i=a[e+"Params"],n=a.callbackScope||t,r&&ht.length&&fa(),i?s.apply(n,i):s.call(n)},kt=255,Ct={aqua:[0,kt,kt],lime:[0,kt,0],silver:[192,192,192],black:[0,0,0],maroon:[128,0,0],teal:[0,128,128],blue:[0,0,kt],navy:[0,0,128],white:[kt,kt,kt],olive:[128,128,0],yellow:[kt,kt,0],orange:[kt,165,0],gray:[128,128,128],purple:[128,0,128],green:[0,128,0],red:[kt,0,0],pink:[kt,192,203],cyan:[0,kt,kt],transparent:[kt,kt,kt,0]},Pt=function(){var t,e="(?:\\b(?:(?:rgb|rgba|hsl|hsla)\\(.+?\\))|\\B#(?:[0-9a-f]{3,4}){1,2}\\b";for(t in Ct)e+="|"+t+"\\b";return new RegExp(e+")","gi")}(),At=/hsl[a]?\(/,St=(x=Date.now,k=500,C=33,A=x(),S=A,z=D=1e3/240,b={time:0,frame:0,tick:function tick(){Ok(!0)},deltaRatio:function deltaRatio(t){return T/(1e3/(t||60))},wake:function wake(){l&&(!n&&u()&&(i=n=window,a=i.document||{},ot.gsap=oe,(i.gsapVersions||(i.gsapVersions=[])).push(oe.version),M(h||i.GreenSockGlobals||!i.gsap&&i||{}),y=i.requestAnimationFrame),m&&b.sleep(),g=y||function(t){return setTimeout(t,z-1e3*b.time+1|0)},c=1,Ok(2))},sleep:function sleep(){(y?i.cancelAnimationFrame:clearTimeout)(m),c=0,g=Q},lagSmoothing:function lagSmoothing(t,e){k=t||1e8,C=Math.min(e,k,0)},fps:function fps(t){D=1e3/(t||240),z=1e3*b.time+D},add:function add(t){F.indexOf(t)<0&&F.push(t),Dt()},remove:function remove(t,e){~(e=F.indexOf(t))&&F.splice(e,1)&&e<=w&&w--},_listeners:F=[]}),Dt=function _wake(){return!c&&St.wake()},zt={},Ft=/^[\d.\-M][\d.\-,\s]/,Et=/["']/g,Bt=function _invertEase(e){return function(t){return 1-e(1-t)}},Rt=function _parseEase(t,e){return t&&(p(t)?t:zt[t]||Fb(t))||e};function Ok(t){var e,r,i,n,a=x()-S,s=!0===t;if(k<a&&(A+=a-C),(0<(e=(i=(S+=a)-A)-z)||s)&&(n=++b.frame,T=i-1e3*b.time,b.time=i/=1e3,z+=e+(D<=e?4:D-e),r=1),s||(m=g(Ok)),r)for(w=0;w<F.length;w++)F[w](i,T,n,t)}function pm(t){return t<B?E*t*t:t<.7272727272727273?E*Math.pow(t-1.5/2.75,2)+.75:t<.9090909090909092?E*(t-=2.25/2.75)*t+.9375:E*Math.pow(t-2.625/2.75,2)+.984375}ba("Linear,Quad,Cubic,Quart,Quint,Strong",function(t,e){var r=e<5?e+1:e;Jb(t+",Power"+(r-1),e?function(t){return Math.pow(t,r)}:function(t){return t},function(t){return 1-Math.pow(1-t,r)},function(t){return t<.5?Math.pow(2*t,r)/2:1-Math.pow(2*(1-t),r)/2})}),zt.Linear.easeNone=zt.none=zt.Linear.easeIn,Jb("Elastic",Lb("in"),Lb("out"),Lb()),E=7.5625,B=1/2.75,Jb("Bounce",function(t){return 1-pm(1-t)},pm),Jb("Expo",function(t){return t?Math.pow(2,10*(t-1)):0}),Jb("Circ",function(t){return-(G(1-t*t)-1)}),Jb("Sine",function(t){return 1===t?1:1-Z(t*V)}),Jb("Back",Mb("in"),Mb("out"),Mb()),zt.SteppedEase=zt.steps=ot.SteppedEase={config:function config(t,e){void 0===t&&(t=1);var r=1/t,i=t+(e?0:1),n=e?1:0;return function(t){return((i*Tt(0,.99999999,t)|0)+n)*r}}},L.ease=zt["quad.out"],ba("onComplete,onUpdate,onStart,onRepeat,onReverseComplete,onInterrupt",function(t){return _t+=t+","+t+"Params,"});var It,Lt=function GSCache(t,e){this.id=J++,(t._gsap=this).target=t,this.harness=e,this.get=e?e.get:aa,this.set=e?e.getSetter:Kt},qt=((It=Animation.prototype).delay=function delay(t){return t||0===t?(this.parent&&this.parent.smoothChildTiming&&this.startTime(this._start+t-this._delay),this._delay=t,this):this._delay},It.duration=function duration(t){return arguments.length?this.totalDuration(0<this._repeat?t+(t+this._rDelay)*this._repeat:t):this.totalDuration()&&this._dur},It.totalDuration=function totalDuration(t){return arguments.length?(this._dirty=0,Ja(this,this._repeat<0?t:(t-this._repeat*this._rDelay)/(this._repeat+1))):this._tDur},It.totalTime=function totalTime(t,e){if(Dt(),!arguments.length)return this._tTime;var r=this._dp;if(r&&r.smoothChildTiming&&this._ts){for(Aa(this,t),!r._dp||r.parent||Ba(r,this);r&&r.parent;)r.parent._time!==r._start+(0<=r._ts?r._tTime/r._ts:(r.totalDuration()-r._tTime)/-r._ts)&&r.totalTime(r._tTime,!0),r=r.parent;!this.parent&&this._dp.autoRemoveChildren&&(0<this._ts&&t<this._tDur||this._ts<0&&0<t||!this._tDur&&!t)&&Ca(this._dp,this,this._start-this._delay)}return(this._tTime!==t||!this._dur&&!e||this._initted&&Math.abs(this._zTime)===X||!t&&!this._initted&&(this.add||this._ptLookup))&&(this._ts||(this._pTime=t),ga(this,t,e)),this},It.time=function time(t,e){return arguments.length?this.totalTime(Math.min(this.totalDuration(),t+wa(this))%(this._dur+this._rDelay)||(t?this._dur:0),e):this._time},It.totalProgress=function totalProgress(t,e){return arguments.length?this.totalTime(this.totalDuration()*t,e):this.totalDuration()?Math.min(1,this._tTime/this._tDur):this.ratio},It.progress=function progress(t,e){return arguments.length?this.totalTime(this.duration()*(!this._yoyo||1&this.iteration()?t:1-t)+wa(this),e):this.duration()?Math.min(1,this._time/this._dur):this.ratio},It.iteration=function iteration(t,e){var r=this.duration()+this._rDelay;return arguments.length?this.totalTime(this._time+(t-1)*r,e):this._repeat?gt(this._tTime,r)+1:1},It.timeScale=function timeScale(t){if(!arguments.length)return this._rts===-X?0:this._rts;if(this._rts===t)return this;var e=this.parent&&this._ts?ya(this.parent._time,this):this._tTime;return this._rts=+t||0,this._ts=this._ps||t===-X?0:this._rts,function _recacheAncestors(t){for(var e=t.parent;e&&e.parent;)e._dirty=1,e.totalDuration(),e=e.parent}(this.totalTime(Tt(-this._delay,this._tDur,e),!0)),za(this),this},It.paused=function paused(t){return arguments.length?(this._ps!==t&&((this._ps=t)?(this._pTime=this._tTime||Math.max(-this._delay,this.rawTime()),this._ts=this._act=0):(Dt(),this._ts=this._rts,this.totalTime(this.parent&&!this.parent.smoothChildTiming?this.rawTime():this._tTime||this._pTime,1===this.progress()&&Math.abs(this._zTime)!==X&&(this._tTime-=X)))),this):this._ps},It.startTime=function startTime(t){if(arguments.length){this._start=t;var e=this.parent||this._dp;return!e||!e._sort&&this.parent||Ca(e,this,t-this._delay),this}return this._start},It.endTime=function endTime(e){return this._start+(t(e)?this.totalDuration():this.duration())/Math.abs(this._ts||1)},It.rawTime=function rawTime(t){var e=this.parent||this._dp;return e?t&&(!this._ts||this._repeat&&this._time&&this.totalProgress()<1)?this._tTime%(this._dur+this._rDelay):this._ts?ya(e.rawTime(t),this):this._tTime:this._tTime},It.globalTime=function globalTime(t){for(var e=this,r=arguments.length?t:e.rawTime();e;)r=e._start+r/(e._ts||1),e=e._dp;return r},It.repeat=function repeat(t){return arguments.length?(this._repeat=t===1/0?-2:t,Ka(this)):-2===this._repeat?1/0:this._repeat},It.repeatDelay=function repeatDelay(t){if(arguments.length){var e=this._time;return this._rDelay=t,Ka(this),e?this.time(e):this}return this._rDelay},It.yoyo=function yoyo(t){return arguments.length?(this._yoyo=t,this):this._yoyo},It.seek=function seek(e,r){return this.totalTime(bt(this,e),t(r))},It.restart=function restart(e,r){return this.play().totalTime(e?-this._delay:0,t(r))},It.play=function play(t,e){return null!=t&&this.seek(t,e),this.reversed(!1).paused(!1)},It.reverse=function reverse(t,e){return null!=t&&this.seek(t||this.totalDuration(),e),this.reversed(!0).paused(!1)},It.pause=function pause(t,e){return null!=t&&this.seek(t,e),this.paused(!0)},It.resume=function resume(){return this.paused(!1)},It.reversed=function reversed(t){return arguments.length?(!!t!==this.reversed()&&this.timeScale(-this._rts||(t?-X:0)),this):this._rts<0},It.invalidate=function invalidate(){return this._initted=this._act=0,this._zTime=-X,this},It.isActive=function isActive(){var t,e=this.parent||this._dp,r=this._start;return!(e&&!(this._ts&&this._initted&&e.isActive()&&(t=e.rawTime(!0))>=r&&t<this.endTime(!0)-X))},It.eventCallback=function eventCallback(t,e,r){var i=this.vars;return 1<arguments.length?(e?(i[t]=e,r&&(i[t+"Params"]=r),"onUpdate"===t&&(this._onUpdate=e)):delete i[t],this):i[t]},It.then=function then(t){var i=this;return new Promise(function(e){function Gn(){var t=i.then;i.then=null,p(r)&&(r=r(i))&&(r.then||r===i)&&(i.then=t),e(r),i.then=t}var r=p(t)?t:ia;i._initted&&1===i.totalProgress()&&0<=i._ts||!i._tTime&&i._ts<0?Gn():i._prom=Gn})},It.kill=function kill(){lb(this)},Animation);function Animation(t){this.vars=t,this._delay=+t.delay||0,(this._repeat=t.repeat===1/0?-2:t.repeat||0)&&(this._rDelay=t.repeatDelay||0,this._yoyo=!!t.yoyo||!!t.yoyoEase),this._ts=1,Ja(this,+t.duration,1,1),this.data=t.data,c||St.wake()}ja(qt.prototype,{_time:0,_start:0,_end:0,_tTime:0,_tDur:0,_dirty:0,_repeat:0,_yoyo:!1,parent:null,_initted:!1,_rDelay:0,_ts:1,_dp:0,ratio:0,_zTime:-X,_prom:0,_ps:!1,_rts:1});var Nt=function(n){function Timeline(e,r){var i;return void 0===e&&(e={}),(i=n.call(this,e)||this).labels={},i.smoothChildTiming=!!e.smoothChildTiming,i.autoRemoveChildren=!!e.autoRemoveChildren,i._sort=t(e.sortChildren),I&&Ca(e.parent||I,_assertThisInitialized(i),r),e.reversed&&i.reverse(),e.paused&&i.paused(!0),e.scrollTrigger&&Da(_assertThisInitialized(i),e.scrollTrigger),i}_inheritsLoose(Timeline,n);var e=Timeline.prototype;return e.to=function to(t,e,r){return Na(0,arguments,this),this},e.from=function from(t,e,r){return Na(1,arguments,this),this},e.fromTo=function fromTo(t,e,r,i){return Na(2,arguments,this),this},e.set=function set(t,e,r){return e.duration=0,e.parent=this,oa(e).repeatDelay||(e.repeat=0),e.immediateRender=!!e.immediateRender,new Jt(t,e,bt(this,r),1),this},e.call=function call(t,e,r){return Ca(this,Jt.delayedCall(0,t,e),r)},e.staggerTo=function staggerTo(t,e,r,i,n,a,s){return r.duration=e,r.stagger=r.stagger||i,r.onComplete=a,r.onCompleteParams=s,r.parent=this,new Jt(t,r,bt(this,n)),this},e.staggerFrom=function staggerFrom(e,r,i,n,a,s,o){return i.runBackwards=1,oa(i).immediateRender=t(i.immediateRender),this.staggerTo(e,r,i,n,a,s,o)},e.staggerFromTo=function staggerFromTo(e,r,i,n,a,s,o,u){return n.startAt=i,oa(n).immediateRender=t(n.immediateRender),this.staggerTo(e,r,n,a,s,o,u)},e.render=function render(t,e,r){var i,n,a,s,o,u,h,l,f,d,p,c,_=this._time,m=this._dirty?this.totalDuration():this._tDur,g=this._dur,v=t<=0?0:da(t),y=this._zTime<0!=t<0&&(this._initted||!g);if(this!==I&&m<v&&0<=t&&(v=m),v!==this._tTime||r||y){if(_!==this._time&&g&&(v+=this._time-_,t+=this._time-_),i=v,f=this._start,u=!(l=this._ts),y&&(g||(_=this._zTime),!t&&e||(this._zTime=t)),this._repeat){if(p=this._yoyo,o=g+this._rDelay,this._repeat<-1&&t<0)return this.totalTime(100*o+t,e,r);if(i=da(v%o),v===m?(s=this._repeat,i=g):((s=~~(v/o))&&s===v/o&&(i=g,s--),g<i&&(i=g)),d=gt(this._tTime,o),!_&&this._tTime&&d!==s&&(d=s),p&&1&s&&(i=g-i,c=1),s!==d&&!this._lock){var b=p&&1&d,T=b===(p&&1&s);if(s<d&&(b=!b),_=b?0:g,this._lock=1,this.render(_||(c?0:da(s*o)),e,!g)._lock=0,this._tTime=v,!e&&this.parent&&Mt(this,"onRepeat"),this.vars.repeatRefresh&&!c&&(this.invalidate()._lock=1),_&&_!==this._time||u!=!this._ts||this.vars.onRepeat&&!this.parent&&!this._act)return this;if(g=this._dur,m=this._tDur,T&&(this._lock=2,_=b?g:-1e-4,this.render(_,!0),this.vars.repeatRefresh&&!c&&this.invalidate()),this._lock=0,!this._ts&&!u)return this;Hb(this,c)}}if(this._hasPause&&!this._forcing&&this._lock<2&&(h=function _findNextPauseTween(t,e,r){var i;if(e<r)for(i=t._first;i&&i._start<=r;){if("isPause"===i.data&&i._start>e)return i;i=i._next}else for(i=t._last;i&&i._start>=r;){if("isPause"===i.data&&i._start<e)return i;i=i._prev}}(this,da(_),da(i)))&&(v-=i-(i=h._start)),this._tTime=v,this._time=i,this._act=!l,this._initted||(this._onUpdate=this.vars.onUpdate,this._initted=1,this._zTime=t,_=0),!_&&i&&!e&&(Mt(this,"onStart"),this._tTime!==v))return this;if(_<=i&&0<=t)for(n=this._first;n;){if(a=n._next,(n._act||i>=n._start)&&n._ts&&h!==n){if(n.parent!==this)return this.render(t,e,r);if(n.render(0<n._ts?(i-n._start)*n._ts:(n._dirty?n.totalDuration():n._tDur)+(i-n._start)*n._ts,e,r),i!==this._time||!this._ts&&!u){h=0,a&&(v+=this._zTime=-X);break}}n=a}else{n=this._last;for(var w=t<0?t:i;n;){if(a=n._prev,(n._act||w<=n._end)&&n._ts&&h!==n){if(n.parent!==this)return this.render(t,e,r);if(n.render(0<n._ts?(w-n._start)*n._ts:(n._dirty?n.totalDuration():n._tDur)+(w-n._start)*n._ts,e,r),i!==this._time||!this._ts&&!u){h=0,a&&(v+=this._zTime=w?-X:X);break}}n=a}}if(h&&!e&&(this.pause(),h.render(_<=i?0:-X)._zTime=_<=i?1:-1,this._ts))return this._start=f,za(this),this.render(t,e,r);this._onUpdate&&!e&&Mt(this,"onUpdate",!0),(v===m&&m>=this.totalDuration()||!v&&_)&&(f!==this._start&&Math.abs(l)===Math.abs(this._ts)||this._lock||(!t&&g||!(v===m&&0<this._ts||!v&&this._ts<0)||sa(this,1),e||t<0&&!_||!v&&!_&&m||(Mt(this,v===m&&0<=t?"onComplete":"onReverseComplete",!0),!this._prom||v<m&&0<this.timeScale()||this._prom())))}return this},e.add=function add(t,e){var r=this;if(q(e)||(e=bt(this,e,t)),!(t instanceof qt)){if(W(t))return t.forEach(function(t){return r.add(t,e)}),this;if(o(t))return this.addLabel(t,e);if(!p(t))return this;t=Jt.delayedCall(0,t)}return this!==t?Ca(this,t,e):this},e.getChildren=function getChildren(t,e,r,i){void 0===t&&(t=!0),void 0===e&&(e=!0),void 0===r&&(r=!0),void 0===i&&(i=-j);for(var n=[],a=this._first;a;)a._start>=i&&(a instanceof Jt?e&&n.push(a):(r&&n.push(a),t&&n.push.apply(n,a.getChildren(!0,e,r)))),a=a._next;return n},e.getById=function getById(t){for(var e=this.getChildren(1,1,1),r=e.length;r--;)if(e[r].vars.id===t)return e[r]},e.remove=function remove(t){return o(t)?this.removeLabel(t):p(t)?this.killTweensOf(t):(ra(this,t),t===this._recent&&(this._recent=this._last),ta(this))},e.totalTime=function totalTime(t,e){return arguments.length?(this._forcing=1,!this._dp&&this._ts&&(this._start=da(St.time-(0<this._ts?t/this._ts:(this.totalDuration()-t)/-this._ts))),n.prototype.totalTime.call(this,t,e),this._forcing=0,this):this._tTime},e.addLabel=function addLabel(t,e){return this.labels[t]=bt(this,e),this},e.removeLabel=function removeLabel(t){return delete this.labels[t],this},e.addPause=function addPause(t,e,r){var i=Jt.delayedCall(0,e||Q,r);return i.data="isPause",this._hasPause=1,Ca(this,i,bt(this,t))},e.removePause=function removePause(t){var e=this._first;for(t=bt(this,t);e;)e._start===t&&"isPause"===e.data&&sa(e),e=e._next},e.killTweensOf=function killTweensOf(t,e,r){for(var i=this.getTweensOf(t,r),n=i.length;n--;)Qt!==i[n]&&i[n].kill(t,e);return this},e.getTweensOf=function getTweensOf(t,e){for(var r,i=[],n=xt(t),a=this._first,s=q(e);a;)a instanceof Jt?ea(a._targets,n)&&(s?(!Qt||a._initted&&a._ts)&&a.globalTime(0)<=e&&a.globalTime(a.totalDuration())>e:!e||a.isActive())&&i.push(a):(r=a.getTweensOf(n,e)).length&&i.push.apply(i,r),a=a._next;return i},e.tweenTo=function tweenTo(t,e){e=e||{};var r,i=this,n=bt(i,t),a=e.startAt,s=e.onStart,o=e.onStartParams,u=e.immediateRender,h=Jt.to(i,ja({ease:e.ease||"none",lazy:!1,immediateRender:!1,time:n,overwrite:"auto",duration:e.duration||Math.abs((n-(a&&"time"in a?a.time:i._time))/i.timeScale())||X,onStart:function onStart(){if(i.pause(),!r){var t=e.duration||Math.abs((n-(a&&"time"in a?a.time:i._time))/i.timeScale());h._dur!==t&&Ja(h,t,0,1).render(h._time,!0,!0),r=1}s&&s.apply(h,o||[])}},e));return u?h.render(0):h},e.tweenFromTo=function tweenFromTo(t,e,r){return this.tweenTo(e,ja({startAt:{time:bt(this,t)}},r))},e.recent=function recent(){return this._recent},e.nextLabel=function nextLabel(t){return void 0===t&&(t=this._time),jb(this,bt(this,t))},e.previousLabel=function previousLabel(t){return void 0===t&&(t=this._time),jb(this,bt(this,t),1)},e.currentLabel=function currentLabel(t){return arguments.length?this.seek(t,!0):this.previousLabel(this._time+X)},e.shiftChildren=function shiftChildren(t,e,r){void 0===r&&(r=0);for(var i,n=this._first,a=this.labels;n;)n._start>=r&&(n._start+=t,n._end+=t),n=n._next;if(e)for(i in a)a[i]>=r&&(a[i]+=t);return ta(this)},e.invalidate=function invalidate(){var t=this._first;for(this._lock=0;t;)t.invalidate(),t=t._next;return n.prototype.invalidate.call(this)},e.clear=function clear(t){void 0===t&&(t=!0);for(var e,r=this._first;r;)e=r._next,this.remove(r),r=e;return this._dp&&(this._time=this._tTime=this._pTime=0),t&&(this.labels={}),ta(this)},e.totalDuration=function totalDuration(t){var e,r,i,n=0,a=this,s=a._last,o=j;if(arguments.length)return a.timeScale((a._repeat<0?a.duration():a.totalDuration())/(a.reversed()?-t:t));if(a._dirty){for(i=a.parent;s;)e=s._prev,s._dirty&&s.totalDuration(),o<(r=s._start)&&a._sort&&s._ts&&!a._lock?(a._lock=1,Ca(a,s,r-s._delay,1)._lock=0):o=r,r<0&&s._ts&&(n-=r,(!i&&!a._dp||i&&i.smoothChildTiming)&&(a._start+=r/a._ts,a._time-=r,a._tTime-=r),a.shiftChildren(-r,!1,-Infinity),o=0),s._end>n&&s._ts&&(n=s._end),s=e;Ja(a,a===I&&a._time>n?a._time:n,1,1),a._dirty=0}return a._tDur},Timeline.updateRoot=function updateRoot(t){if(I._ts&&(ga(I,ya(t,I)),f=St.frame),St.frame>=pt){pt+=Y.autoSleep||120;var e=I._first;if((!e||!e._ts)&&Y.autoSleep&&St._listeners.length<2){for(;e&&!e._ts;)e=e._next;e||St.sleep()}}},Timeline}(qt);ja(Nt.prototype,{_lock:0,_hasPause:0,_forcing:0});function Tb(t,e,r,i,n,a){var u,h,l,f;if(ft[t]&&!1!==(u=new ft[t]).init(n,u.rawVars?e[t]:function _processVars(t,e,r,i,n){if(p(t)&&(t=Xt(t,n,e,r,i)),!s(t)||t.style&&t.nodeType||W(t)||H(t))return o(t)?Xt(t,n,e,r,i):t;var a,u={};for(a in t)u[a]=Xt(t[a],n,e,r,i);return u}(e[t],i,n,a,r),r,i,a)&&(r._pt=h=new ae(r._pt,n,t,0,1,u.render,u,0,u.priority),r!==d))for(l=r._ptLookup[r._targets.indexOf(n)],f=u._props.length;f--;)l[u._props[f]]=h;return u}function Xb(t,r,e,i){var n,a,s=r.ease||i||"power1.inOut";if(W(r))a=e[t]||(e[t]=[]),r.forEach(function(t,e){return a.push({t:e/(r.length-1)*100,v:t,e:s})});else for(n in r)a=e[n]||(e[n]=[]),"ease"===n||a.push({t:parseFloat(t),v:r[n],e:s})}var Qt,Yt=function _addPropTween(t,e,r,i,n,a,s,u,h){p(i)&&(i=i(n||0,t,a));var l,f=t[e],d="get"!==r?r:p(f)?h?t[e.indexOf("set")||!p(t["get"+e.substr(3)])?e:"get"+e.substr(3)](h):t[e]():f,c=p(f)?h?Zt:$t:Gt;if(o(i)&&(~i.indexOf("random(")&&(i=gb(i)),"="===i.charAt(1)&&(!(l=parseFloat(d)+parseFloat(i.substr(2))*("-"===i.charAt(0)?-1:1)+(Qa(d)||0))&&0!==l||(i=l))),d!==i)return isNaN(d*i)||""===i?(f||e in t||N(e,i),function _addComplexStringPropTween(t,e,r,i,n,a,s){var o,u,h,l,f,d,p,c,_=new ae(this._pt,t,e,0,1,te,null,n),m=0,g=0;for(_.b=r,_.e=i,r+="",(p=~(i+="").indexOf("random("))&&(i=gb(i)),a&&(a(c=[r,i],t,e),r=c[0],i=c[1]),u=r.match(it)||[];o=it.exec(i);)l=o[0],f=i.substring(m,o.index),h?h=(h+1)%5:"rgba("===f.substr(-5)&&(h=1),l!==u[g++]&&(d=parseFloat(u[g-1])||0,_._pt={_next:_._pt,p:f||1===g?f:",",s:d,c:"="===l.charAt(1)?parseFloat(l.substr(2))*("-"===l.charAt(0)?-1:1):parseFloat(l)-d,m:h&&h<4?Math.round:0},m=it.lastIndex);return _.c=m<i.length?i.substring(m,i.length):"",_.fp=s,(nt.test(i)||p)&&(_.e=0),this._pt=_}.call(this,t,e,d,i,c,u||Y.stringFilter,h)):(l=new ae(this._pt,t,e,+d||0,i-(d||0),"boolean"==typeof f?Wt:Ht,0,c),h&&(l.fp=h),s&&l.modifier(s,this,t),this._pt=l)},jt=function _initTween(e,r){var i,n,a,s,o,u,h,l,f,d,p,c,m,g=e.vars,v=g.ease,y=g.startAt,b=g.immediateRender,T=g.lazy,w=g.onUpdate,x=g.onUpdateParams,O=g.callbackScope,M=g.runBackwards,k=g.yoyoEase,C=g.keyframes,P=g.autoRevert,A=e._dur,S=e._startAt,D=e._targets,z=e.parent,F=z&&"nested"===z.data?z.parent._targets:D,E="auto"===e._overwrite&&!R,B=e.timeline;if(!B||C&&v||(v="none"),e._ease=Rt(v,L.ease),e._yEase=k?Bt(Rt(!0===k?v:k,L.ease)):0,k&&e._yoyo&&!e._repeat&&(k=e._yEase,e._yEase=e._ease,e._ease=k),e._from=!B&&!!g.runBackwards,!B||C&&!g.stagger){if(c=(l=D[0]?_(D[0]).harness:0)&&g[l.prop],i=na(g,ut),S&&sa(S.render(-1,!0)),y)if(sa(e._startAt=Jt.set(D,ja({data:"isStart",overwrite:!1,parent:z,immediateRender:!0,lazy:t(T),startAt:null,delay:0,onUpdate:w,onUpdateParams:x,callbackScope:O,stagger:0},y))),r<0&&!b&&!P&&e._startAt.render(-1,!0),b){if(0<r&&!P&&(e._startAt=0),A&&r<=0)return void(r&&(e._zTime=r))}else!1===P&&(e._startAt=0);else if(M&&A)if(S)P||(e._startAt=0);else if(r&&(b=!1),a=ja({overwrite:!1,data:"isFromStart",lazy:b&&t(T),immediateRender:b,stagger:0,parent:z},i),c&&(a[l.prop]=c),sa(e._startAt=Jt.set(D,a)),r<0&&e._startAt.render(-1,!0),e._zTime=r,b){if(!r)return}else _initTween(e._startAt,X);for(e._pt=0,T=A&&t(T)||T&&!A,n=0;n<D.length;n++){if(h=(o=D[n])._gsap||$(D)[n]._gsap,e._ptLookup[n]=d={},lt[h.id]&&ht.length&&fa(),p=F===D?n:F.indexOf(o),l&&!1!==(f=new l).init(o,c||i,e,p,F)&&(e._pt=s=new ae(e._pt,o,f.name,0,1,f.render,f,0,f.priority),f._props.forEach(function(t){d[t]=s}),f.priority&&(u=1)),!l||c)for(a in i)ft[a]&&(f=Tb(a,i,e,p,o,F))?f.priority&&(u=1):d[a]=s=Yt.call(e,o,a,"get",i[a],p,F,0,g.stringFilter);e._op&&e._op[n]&&e.kill(o,e._op[n]),E&&e._pt&&(Qt=e,I.killTweensOf(o,d,e.globalTime(r)),m=!e.parent,Qt=0),e._pt&&T&&(lt[h.id]=1)}u&&ne(e),e._onInit&&e._onInit(e)}e._onUpdate=w,e._initted=(!e._op||e._pt)&&!m,C&&r<=0&&B.render(j,!0,!0)},Xt=function _parseFuncOrString(t,e,r,i,n){return p(t)?t.call(e,r,i,n):o(t)&&~t.indexOf("random(")?gb(t):t},Ut=_t+"repeat,repeatDelay,yoyo,repeatRefresh,yoyoEase",Vt={};ba(Ut+",id,stagger,delay,duration,paused,scrollTrigger",function(t){return Vt[t]=1});var Jt=function(F){function Tween(e,r,i,n){var a;"number"==typeof r&&(i.duration=r,r=i,i=null);var o,u,h,l,f,d,p,c,_=(a=F.call(this,n?r:oa(r))||this).vars,m=_.duration,g=_.delay,y=_.immediateRender,b=_.stagger,T=_.overwrite,w=_.keyframes,x=_.defaults,M=_.scrollTrigger,k=_.yoyoEase,C=r.parent||I,P=(W(e)||H(e)?q(e[0]):"length"in r)?[e]:xt(e);if(a._targets=P.length?$(P):O("GSAP target "+e+" not found. https://greensock.com",!Y.nullTargetWarn)||[],a._ptLookup=[],a._overwrite=T,w||b||v(m)||v(g)){if(r=a.vars,(o=a.timeline=new Nt({data:"nested",defaults:x||{}})).kill(),o.parent=o._dp=_assertThisInitialized(a),o._start=0,b||v(m)||v(g)){if(l=P.length,p=b&&Ya(b),s(b))for(f in b)~Ut.indexOf(f)&&((c=c||{})[f]=b[f]);for(u=0;u<l;u++)(h=na(r,Vt)).stagger=0,k&&(h.yoyoEase=k),c&&mt(h,c),d=P[u],h.duration=+Xt(m,_assertThisInitialized(a),u,d,P),h.delay=(+Xt(g,_assertThisInitialized(a),u,d,P)||0)-a._delay,!b&&1===l&&h.delay&&(a._delay=g=h.delay,a._start+=g,h.delay=0),o.to(d,h,p?p(u,d,P):0),o._ease=zt.none;o.duration()?m=g=0:a.timeline=0}else if(w){oa(ja(o.vars.defaults,{ease:"none"})),o._ease=Rt(w.ease||r.ease||"none");var A,S,D,z=0;if(W(w))w.forEach(function(t){return o.to(P,t,">")});else{for(f in h={},w)"ease"===f||"easeEach"===f||Xb(f,w[f],h,w.easeEach);for(f in h)for(A=h[f].sort(function(t,e){return t.t-e.t}),u=z=0;u<A.length;u++)(D={ease:(S=A[u]).e,duration:(S.t-(u?A[u-1].t:0))/100*m})[f]=S.v,o.to(P,D,z),z+=D.duration;o.duration()<m&&o.to({},{duration:m-o.duration()})}}m||a.duration(m=o.duration())}else a.timeline=0;return!0!==T||R||(Qt=_assertThisInitialized(a),I.killTweensOf(P),Qt=0),Ca(C,_assertThisInitialized(a),i),r.reversed&&a.reverse(),r.paused&&a.paused(!0),(y||!m&&!w&&a._start===da(C._time)&&t(y)&&function _hasNoPausedAncestors(t){return!t||t._ts&&_hasNoPausedAncestors(t.parent)}(_assertThisInitialized(a))&&"nested"!==C.data)&&(a._tTime=-X,a.render(Math.max(0,-g))),M&&Da(_assertThisInitialized(a),M),a}_inheritsLoose(Tween,F);var e=Tween.prototype;return e.render=function render(t,e,r){var i,n,a,s,o,u,h,l,f,d=this._time,p=this._tDur,c=this._dur,_=p-X<t&&0<=t?p:t<X?0:t;if(c){if(_!==this._tTime||!t||r||!this._initted&&this._tTime||this._startAt&&this._zTime<0!=t<0){if(i=_,l=this.timeline,this._repeat){if(s=c+this._rDelay,this._repeat<-1&&t<0)return this.totalTime(100*s+t,e,r);if(i=da(_%s),_===p?(a=this._repeat,i=c):((a=~~(_/s))&&a===_/s&&(i=c,a--),c<i&&(i=c)),(u=this._yoyo&&1&a)&&(f=this._yEase,i=c-i),o=gt(this._tTime,s),i===d&&!r&&this._initted)return this;a!==o&&(l&&this._yEase&&Hb(l,u),!this.vars.repeatRefresh||u||this._lock||(this._lock=r=1,this.render(da(s*a),!0).invalidate()._lock=0))}if(!this._initted){if(Ea(this,t<0?t:i,r,e))return this._tTime=0,this;if(c!==this._dur)return this.render(t,e,r)}if(this._tTime=_,this._time=i,!this._act&&this._ts&&(this._act=1,this._lazy=0),this.ratio=h=(f||this._ease)(i/c),this._from&&(this.ratio=h=1-h),i&&!d&&!e&&(Mt(this,"onStart"),this._tTime!==_))return this;for(n=this._pt;n;)n.r(h,n.d),n=n._next;l&&l.render(t<0?t:!i&&u?-X:l._dur*l._ease(i/this._dur),e,r)||this._startAt&&(this._zTime=t),this._onUpdate&&!e&&(t<0&&this._startAt&&this._startAt.render(t,!0,r),Mt(this,"onUpdate")),this._repeat&&a!==o&&this.vars.onRepeat&&!e&&this.parent&&Mt(this,"onRepeat"),_!==this._tDur&&_||this._tTime!==_||(t<0&&this._startAt&&!this._onUpdate&&this._startAt.render(t,!0,!0),!t&&c||!(_===this._tDur&&0<this._ts||!_&&this._ts<0)||sa(this,1),e||t<0&&!d||!_&&!d||(Mt(this,_===p?"onComplete":"onReverseComplete",!0),!this._prom||_<p&&0<this.timeScale()||this._prom()))}}else!function _renderZeroDurationTween(t,e,r,i){var n,a,s,o=t.ratio,u=e<0||!e&&(!t._start&&function _parentPlayheadIsBeforeStart(t){var e=t.parent;return e&&e._ts&&e._initted&&!e._lock&&(e.rawTime()<0||_parentPlayheadIsBeforeStart(e))}(t)&&(t._initted||!vt(t))||(t._ts<0||t._dp._ts<0)&&!vt(t))?0:1,h=t._rDelay,l=0;if(h&&t._repeat&&(l=Tt(0,t._tDur,e),a=gt(l,h),t._yoyo&&1&a&&(u=1-u),a!==gt(t._tTime,h)&&(o=1-u,t.vars.repeatRefresh&&t._initted&&t.invalidate())),u!==o||i||t._zTime===X||!e&&t._zTime){if(!t._initted&&Ea(t,e,i,r))return;for(s=t._zTime,t._zTime=e||(r?X:0),r=r||e&&!s,t.ratio=u,t._from&&(u=1-u),t._time=0,t._tTime=l,n=t._pt;n;)n.r(u,n.d),n=n._next;t._startAt&&e<0&&t._startAt.render(e,!0,!0),t._onUpdate&&!r&&Mt(t,"onUpdate"),l&&t._repeat&&!r&&t.parent&&Mt(t,"onRepeat"),(e>=t._tDur||e<0)&&t.ratio===u&&(u&&sa(t,1),r||(Mt(t,u?"onComplete":"onReverseComplete",!0),t._prom&&t._prom()))}else t._zTime||(t._zTime=e)}(this,t,e,r);return this},e.targets=function targets(){return this._targets},e.invalidate=function invalidate(){return this._pt=this._op=this._startAt=this._onUpdate=this._lazy=this.ratio=0,this._ptLookup=[],this.timeline&&this.timeline.invalidate(),F.prototype.invalidate.call(this)},e.kill=function kill(t,e){if(void 0===e&&(e="all"),!(t||e&&"all"!==e))return this._lazy=this._pt=0,this.parent?lb(this):this;if(this.timeline){var r=this.timeline.totalDuration();return this.timeline.killTweensOf(t,e,Qt&&!0!==Qt.vars.overwrite)._first||lb(this),this.parent&&r!==this.timeline.totalDuration()&&Ja(this,this._dur*this.timeline._tDur/r,0,1),this}var i,n,a,s,u,h,l,f=this._targets,d=t?xt(t):f,p=this._ptLookup,c=this._pt;if((!e||"all"===e)&&function _arraysMatch(t,e){for(var r=t.length,i=r===e.length;i&&r--&&t[r]===e[r];);return r<0}(f,d))return"all"===e&&(this._pt=0),lb(this);for(i=this._op=this._op||[],"all"!==e&&(o(e)&&(u={},ba(e,function(t){return u[t]=1}),e=u),e=function _addAliasesToVars(t,e){var r,i,n,a,s=t[0]?_(t[0]).harness:0,o=s&&s.aliases;if(!o)return e;for(i in r=mt({},e),o)if(i in r)for(n=(a=o[i].split(",")).length;n--;)r[a[n]]=r[i];return r}(f,e)),l=f.length;l--;)if(~d.indexOf(f[l]))for(u in n=p[l],"all"===e?(i[l]=e,s=n,a={}):(a=i[l]=i[l]||{},s=e),s)(h=n&&n[u])&&("kill"in h.d&&!0!==h.d.kill(u)||ra(this,h,"_pt"),delete n[u]),"all"!==a&&(a[u]=1);return this._initted&&!this._pt&&c&&lb(this),this},Tween.to=function to(t,e,r){return new Tween(t,e,r)},Tween.from=function from(t,e){return Na(1,arguments)},Tween.delayedCall=function delayedCall(t,e,r,i){return new Tween(e,0,{immediateRender:!1,lazy:!1,overwrite:!1,delay:t,onComplete:e,onReverseComplete:e,onCompleteParams:r,onReverseCompleteParams:r,callbackScope:i})},Tween.fromTo=function fromTo(t,e,r){return Na(2,arguments)},Tween.set=function set(t,e){return e.duration=0,e.repeatDelay||(e.repeat=0),new Tween(t,e)},Tween.killTweensOf=function killTweensOf(t,e,r){return I.killTweensOf(t,e,r)},Tween}(qt);ja(Jt.prototype,{_targets:[],_lazy:0,_startAt:0,_op:0,_onInit:0}),ba("staggerTo,staggerFrom,staggerFromTo",function(r){Jt[r]=function(){var t=new Nt,e=wt.call(arguments,0);return e.splice("staggerFromTo"===r?5:4,0,0),t[r].apply(t,e)}});function dc(t,e,r){return t.setAttribute(e,r)}function lc(t,e,r,i){i.mSet(t,e,i.m.call(i.tween,r,i.mt),i)}var Gt=function _setterPlain(t,e,r){return t[e]=r},$t=function _setterFunc(t,e,r){return t[e](r)},Zt=function _setterFuncWithParam(t,e,r,i){return t[e](i.fp,r)},Kt=function _getSetter(t,e){return p(t[e])?$t:r(t[e])&&t.setAttribute?dc:Gt},Ht=function _renderPlain(t,e){return e.set(e.t,e.p,Math.round(1e6*(e.s+e.c*t))/1e6,e)},Wt=function _renderBoolean(t,e){return e.set(e.t,e.p,!!(e.s+e.c*t),e)},te=function _renderComplexString(t,e){var r=e._pt,i="";if(!t&&e.b)i=e.b;else if(1===t&&e.e)i=e.e;else{for(;r;)i=r.p+(r.m?r.m(r.s+r.c*t):Math.round(1e4*(r.s+r.c*t))/1e4)+i,r=r._next;i+=e.c}e.set(e.t,e.p,i,e)},ee=function _renderPropTweens(t,e){for(var r=e._pt;r;)r.r(t,r.d),r=r._next},re=function _addPluginModifier(t,e,r,i){for(var n,a=this._pt;a;)n=a._next,a.p===i&&a.modifier(t,e,r),a=n},ie=function _killPropTweensOf(t){for(var e,r,i=this._pt;i;)r=i._next,i.p===t&&!i.op||i.op===t?ra(this,i,"_pt"):i.dep||(e=1),i=r;return!e},ne=function _sortPropTweensByPriority(t){for(var e,r,i,n,a=t._pt;a;){for(e=a._next,r=i;r&&r.pr>a.pr;)r=r._next;(a._prev=r?r._prev:n)?a._prev._next=a:i=a,(a._next=r)?r._prev=a:n=a,a=e}t._pt=i},ae=(PropTween.prototype.modifier=function modifier(t,e,r){this.mSet=this.mSet||this.set,this.set=lc,this.m=t,this.mt=r,this.tween=e},PropTween);function PropTween(t,e,r,i,n,a,s,o,u){this.t=e,this.s=i,this.c=n,this.p=r,this.r=a||Ht,this.d=s||this,this.set=o||Gt,this.pr=u||0,(this._next=t)&&(t._prev=this)}ba(_t+"parent,duration,ease,delay,overwrite,runBackwards,startAt,yoyo,immediateRender,repeat,repeatDelay,data,paused,reversed,lazy,callbackScope,stringFilter,id,yoyoEase,stagger,inherit,repeatRefresh,keyframes,autoRevert,scrollTrigger",function(t){return ut[t]=1}),ot.TweenMax=ot.TweenLite=Jt,ot.TimelineLite=ot.TimelineMax=Nt,I=new Nt({sortChildren:!1,defaults:L,autoRemoveChildren:!0,id:"root",smoothChildTiming:!0}),Y.stringFilter=wb;var se={registerPlugin:function registerPlugin(){for(var t=arguments.length,e=new Array(t),r=0;r<t;r++)e[r]=arguments[r];e.forEach(function(t){return function _createPlugin(t){var e=(t=!t.name&&t.default||t).name,r=p(t),i=e&&!r&&t.init?function(){this._props=[]}:t,n={init:Q,render:ee,add:Yt,kill:ie,modifier:re,rawVars:0},a={targetTest:0,get:0,getSetter:Kt,aliases:{},register:0};if(Dt(),t!==i){if(ft[e])return;ja(i,ja(na(t,n),a)),mt(i.prototype,mt(n,na(t,a))),ft[i.prop=e]=i,t.targetTest&&(ct.push(i),ut[e]=1),e=("css"===e?"CSS":e.charAt(0).toUpperCase()+e.substr(1))+"Plugin"}P(e,i),t.register&&t.register(oe,i,ae)}(t)})},timeline:function timeline(t){return new Nt(t)},getTweensOf:function getTweensOf(t,e){return I.getTweensOf(t,e)},getProperty:function getProperty(i,t,e,r){o(i)&&(i=xt(i)[0]);var n=_(i||{}).get,a=e?ia:ha;return"native"===e&&(e=""),i?t?a((ft[t]&&ft[t].get||n)(i,t,e,r)):function(t,e,r){return a((ft[t]&&ft[t].get||n)(i,t,e,r))}:i},quickSetter:function quickSetter(r,e,i){if(1<(r=xt(r)).length){var n=r.map(function(t){return oe.quickSetter(t,e,i)}),a=n.length;return function(t){for(var e=a;e--;)n[e](t)}}r=r[0]||{};var s=ft[e],o=_(r),u=o.harness&&(o.harness.aliases||{})[e]||e,h=s?function(t){var e=new s;d._pt=0,e.init(r,i?t+i:t,d,0,[r]),e.render(1,e),d._pt&&ee(1,d)}:o.set(r,u);return s?h:function(t){return h(r,u,i?t+i:t,o,1)}},isTweening:function isTweening(t){return 0<I.getTweensOf(t,!0).length},defaults:function defaults(t){return t&&t.ease&&(t.ease=Rt(t.ease,L.ease)),ma(L,t||{})},config:function config(t){return ma(Y,t||{})},registerEffect:function registerEffect(t){var i=t.name,n=t.effect,e=t.plugins,a=t.defaults,r=t.extendTimeline;(e||"").split(",").forEach(function(t){return t&&!ft[t]&&!ot[t]&&O(i+" effect requires "+t+" plugin.")}),dt[i]=function(t,e,r){return n(xt(t),ja(e||{},a),r)},r&&(Nt.prototype[i]=function(t,e,r){return this.add(dt[i](t,s(e)?e:(r=e)&&{},this),r)})},registerEase:function registerEase(t,e){zt[t]=Rt(e)},parseEase:function parseEase(t,e){return arguments.length?Rt(t,e):zt},getById:function getById(t){return I.getById(t)},exportRoot:function exportRoot(e,r){void 0===e&&(e={});var i,n,a=new Nt(e);for(a.smoothChildTiming=t(e.smoothChildTiming),I.remove(a),a._dp=0,a._time=a._tTime=I._time,i=I._first;i;)n=i._next,!r&&!i._dur&&i instanceof Jt&&i.vars.onComplete===i._targets[0]||Ca(a,i,i._start-i._delay),i=n;return Ca(I,a,0),a},utils:{wrap:function wrap(e,t,r){var i=t-e;return W(e)?db(e,wrap(0,e.length),t):Oa(r,function(t){return(i+(t-e)%i)%i+e})},wrapYoyo:function wrapYoyo(e,t,r){var i=t-e,n=2*i;return W(e)?db(e,wrapYoyo(0,e.length-1),t):Oa(r,function(t){return e+(i<(t=(n+(t-e)%n)%n||0)?n-t:t)})},distribute:Ya,random:_a,snap:$a,normalize:function normalize(t,e,r){return Ot(t,e,0,1,r)},getUnit:Qa,clamp:function clamp(e,r,t){return Oa(t,function(t){return Tt(e,r,t)})},splitColor:rb,toArray:xt,selector:function selector(r){return r=xt(r)[0]||O("Invalid scope")||{},function(t){var e=r.current||r.nativeElement||r;return xt(t,e.querySelectorAll?e:e===r?O("Invalid scope")||a.createElement("div"):r)}},mapRange:Ot,pipe:function pipe(){for(var t=arguments.length,e=new Array(t),r=0;r<t;r++)e[r]=arguments[r];return function(t){return e.reduce(function(t,e){return e(t)},t)}},unitize:function unitize(e,r){return function(t){return e(parseFloat(t))+(r||Qa(t))}},interpolate:function interpolate(e,r,t,i){var n=isNaN(e+r)?0:function(t){return(1-t)*e+t*r};if(!n){var a,s,u,h,l,f=o(e),d={};if(!0===t&&(i=1)&&(t=null),f)e={p:e},r={p:r};else if(W(e)&&!W(r)){for(u=[],h=e.length,l=h-2,s=1;s<h;s++)u.push(interpolate(e[s-1],e[s]));h--,n=function func(t){t*=h;var e=Math.min(l,~~t);return u[e](t-e)},t=r}else i||(e=mt(W(e)?[]:{},e));if(!u){for(a in r)Yt.call(d,e,a,"get",r[a]);n=function func(t){return ee(t,d)||(f?e.p:e)}}}return Oa(t,n)},shuffle:Xa},install:M,effects:dt,ticker:St,updateRoot:Nt.updateRoot,plugins:ft,globalTimeline:I,core:{PropTween:ae,globals:P,Tween:Jt,Timeline:Nt,Animation:qt,getCache:_,_removeLinkedListItem:ra,suppressOverwrites:function suppressOverwrites(t){return R=t}}};ba("to,from,fromTo,delayedCall,set,killTweensOf",function(t){return se[t]=Jt[t]}),St.add(Nt.updateRoot),d=se.to({},{duration:0});function pc(t,e){for(var r=t._pt;r&&r.p!==e&&r.op!==e&&r.fp!==e;)r=r._next;return r}function rc(t,n){return{name:t,rawVars:1,init:function init(t,i,e){e._onInit=function(t){var e,r;if(o(i)&&(e={},ba(i,function(t){return e[t]=1}),i=e),n){for(r in e={},i)e[r]=n(i[r]);i=e}!function _addModifiers(t,e){var r,i,n,a=t._targets;for(r in e)for(i=a.length;i--;)(n=(n=t._ptLookup[i][r])&&n.d)&&(n._pt&&(n=pc(n,r)),n&&n.modifier&&n.modifier(e[r],t,a[i],r))}(t,i)}}}}var oe=se.registerPlugin({name:"attr",init:function init(t,e,r,i,n){var a,s;for(a in e)(s=this.add(t,"setAttribute",(t.getAttribute(a)||0)+"",e[a],i,n,0,0,a))&&(s.op=a),this._props.push(a)}},{name:"endArray",init:function init(t,e){for(var r=e.length;r--;)this.add(t,r,t[r]||0,e[r])}},rc("roundProps",Za),rc("modifiers"),rc("snap",$a))||se;Jt.version=Nt.version=oe.version="3.9.1",l=1,u()&&Dt();function ad(t,e){return e.set(e.t,e.p,Math.round(1e4*(e.s+e.c*t))/1e4+e.u,e)}function bd(t,e){return e.set(e.t,e.p,1===t?e.e:Math.round(1e4*(e.s+e.c*t))/1e4+e.u,e)}function cd(t,e){return e.set(e.t,e.p,t?Math.round(1e4*(e.s+e.c*t))/1e4+e.u:e.b,e)}function dd(t,e){var r=e.s+e.c*t;e.set(e.t,e.p,~~(r+(r<0?-.5:.5))+e.u,e)}function ed(t,e){return e.set(e.t,e.p,t?e.e:e.b,e)}function fd(t,e){return e.set(e.t,e.p,1!==t?e.b:e.e,e)}function gd(t,e,r){return t.style[e]=r}function hd(t,e,r){return t.style.setProperty(e,r)}function id(t,e,r){return t._gsap[e]=r}function jd(t,e,r){return t._gsap.scaleX=t._gsap.scaleY=r}function kd(t,e,r,i,n){var a=t._gsap;a.scaleX=a.scaleY=r,a.renderTransform(n,a)}function ld(t,e,r,i,n){var a=t._gsap;a[e]=r,a.renderTransform(n,a)}function pd(t,e){var r=he.createElementNS?he.createElementNS((e||"http://www.w3.org/1999/xhtml").replace(/^https/,"http"),t):he.createElement(t);return r.style?r:he.createElement(t)}function qd(t,e,r){var i=getComputedStyle(t);return i[e]||i.getPropertyValue(e.replace(Ie,"-$1").toLowerCase())||i.getPropertyValue(e)||!r&&qd(t,Xe(e)||e,1)||""}function td(){(function _windowExists(){return"undefined"!=typeof window})()&&window.document&&(ue=window,he=ue.document,le=he.documentElement,de=pd("div")||{style:{}},pd("div"),Qe=Xe(Qe),Ye=Qe+"Origin",de.style.cssText="border-width:0;line-height:0;position:absolute;padding:0",ce=!!Xe("perspective"),fe=1)}function ud(t){var e,r=pd("svg",this.ownerSVGElement&&this.ownerSVGElement.getAttribute("xmlns")||"http://www.w3.org/2000/svg"),i=this.parentNode,n=this.nextSibling,a=this.style.cssText;if(le.appendChild(r),r.appendChild(this),this.style.display="block",t)try{e=this.getBBox(),this._gsapBBox=this.getBBox,this.getBBox=ud}catch(t){}else this._gsapBBox&&(e=this._gsapBBox());return i&&(n?i.insertBefore(this,n):i.appendChild(this)),le.removeChild(r),this.style.cssText=a,e}function vd(t,e){for(var r=e.length;r--;)if(t.hasAttribute(e[r]))return t.getAttribute(e[r])}function wd(e){var r;try{r=e.getBBox()}catch(t){r=ud.call(e,!0)}return r&&(r.width||r.height)||e.getBBox===ud||(r=ud.call(e,!0)),!r||r.width||r.x||r.y?r:{x:+vd(e,["x","cx","x1"])||0,y:+vd(e,["y","cy","y1"])||0,width:0,height:0}}function xd(t){return!(!t.getCTM||t.parentNode&&!t.ownerSVGElement||!wd(t))}function yd(t,e){if(e){var r=t.style;e in Fe&&e!==Ye&&(e=Qe),r.removeProperty?("ms"!==e.substr(0,2)&&"webkit"!==e.substr(0,6)||(e="-"+e),r.removeProperty(e.replace(Ie,"-$1").toLowerCase())):r.removeAttribute(e)}}function zd(t,e,r,i,n,a){var s=new ae(t._pt,e,r,0,1,a?fd:ed);return(t._pt=s).b=i,s.e=n,t._props.push(r),s}function Bd(t,e,r,i){var n,a,s,o,u=parseFloat(r)||0,h=(r+"").trim().substr((u+"").length)||"px",l=de.style,f=Le.test(e),d="svg"===t.tagName.toLowerCase(),p=(d?"client":"offset")+(f?"Width":"Height"),c="px"===i,m="%"===i;return i===h||!u||Ue[i]||Ue[h]?u:("px"===h||c||(u=Bd(t,e,r,"px")),o=t.getCTM&&xd(t),!m&&"%"!==h||!Fe[e]&&!~e.indexOf("adius")?(l[f?"width":"height"]=100+(c?h:i),a=~e.indexOf("adius")||"em"===i&&t.appendChild&&!d?t:t.parentNode,o&&(a=(t.ownerSVGElement||{}).parentNode),a&&a!==he&&a.appendChild||(a=he.body),(s=a._gsap)&&m&&s.width&&f&&s.time===St.time?ca(u/s.width*100):(!m&&"%"!==h||(l.position=qd(t,"position")),a===t&&(l.position="static"),a.appendChild(de),n=de[p],a.removeChild(de),l.position="absolute",f&&m&&((s=_(a)).time=St.time,s.width=a[p]),ca(c?n*u/100:n&&u?100/n*u:0))):(n=o?t.getBBox()[f?"width":"height"]:t[p],ca(m?u/n*100:u/100*n)))}function Cd(t,e,r,i){var n;return fe||td(),e in Ne&&"transform"!==e&&~(e=Ne[e]).indexOf(",")&&(e=e.split(",")[0]),Fe[e]&&"transform"!==e?(n=Ze(t,i),n="transformOrigin"!==e?n[e]:n.svg?n.origin:Ke(qd(t,Ye))+" "+n.zOrigin+"px"):(n=t.style[e])&&"auto"!==n&&!i&&!~(n+"").indexOf("calc(")||(n=Je[e]&&Je[e](t,e,r)||qd(t,e)||aa(t,e)||("opacity"===e?1:0)),r&&!~(n+"").trim().indexOf(" ")?Bd(t,e,n,r)+r:n}function Dd(t,e,r,i){if(!r||"none"===r){var n=Xe(e,t,1),a=n&&qd(t,n,1);a&&a!==r?(e=n,r=a):"borderColor"===e&&(r=qd(t,"borderTopColor"))}var s,o,u,h,l,f,d,p,c,_,m,g,v=new ae(this._pt,t.style,e,0,1,te),y=0,b=0;if(v.b=r,v.e=i,r+="","auto"===(i+="")&&(t.style[e]=i,i=qd(t,e)||i,t.style[e]=r),wb(s=[r,i]),i=s[1],u=(r=s[0]).match(rt)||[],(i.match(rt)||[]).length){for(;o=rt.exec(i);)d=o[0],c=i.substring(y,o.index),l?l=(l+1)%5:"rgba("!==c.substr(-5)&&"hsla("!==c.substr(-5)||(l=1),d!==(f=u[b++]||"")&&(h=parseFloat(f)||0,m=f.substr((h+"").length),(g="="===d.charAt(1)?+(d.charAt(0)+"1"):0)&&(d=d.substr(2)),p=parseFloat(d),_=d.substr((p+"").length),y=rt.lastIndex-_.length,_||(_=_||Y.units[e]||m,y===i.length&&(i+=_,v.e+=_)),m!==_&&(h=Bd(t,e,f,_)||0),v._pt={_next:v._pt,p:c||1===b?c:",",s:h,c:g?g*p:p-h,m:l&&l<4||"zIndex"===e?Math.round:0});v.c=y<i.length?i.substring(y,i.length):""}else v.r="display"===e&&"none"===i?fd:ed;return nt.test(i)&&(v.e=0),this._pt=v}function Fd(t){var e=t.split(" "),r=e[0],i=e[1]||"50%";return"top"!==r&&"bottom"!==r&&"left"!==i&&"right"!==i||(t=r,r=i,i=t),e[0]=Ve[r]||r,e[1]=Ve[i]||i,e.join(" ")}function Gd(t,e){if(e.tween&&e.tween._time===e.tween._dur){var r,i,n,a=e.t,s=a.style,o=e.u,u=a._gsap;if("all"===o||!0===o)s.cssText="",i=1;else for(n=(o=o.split(",")).length;-1<--n;)r=o[n],Fe[r]&&(i=1,r="transformOrigin"===r?Ye:Qe),yd(a,r);i&&(yd(a,Qe),u&&(u.svg&&a.removeAttribute("transform"),Ze(a,1),u.uncache=1))}}function Kd(t){return"matrix(1, 0, 0, 1, 0, 0)"===t||"none"===t||!t}function Ld(t){var e=qd(t,Qe);return Kd(e)?Ge:e.substr(7).match(et).map(ca)}function Md(t,e){var r,i,n,a,s=t._gsap||_(t),o=t.style,u=Ld(t);return s.svg&&t.getAttribute("transform")?"1,0,0,1,0,0"===(u=[(n=t.transform.baseVal.consolidate().matrix).a,n.b,n.c,n.d,n.e,n.f]).join(",")?Ge:u:(u!==Ge||t.offsetParent||t===le||s.svg||(n=o.display,o.display="block",(r=t.parentNode)&&t.offsetParent||(a=1,i=t.nextSibling,le.appendChild(t)),u=Ld(t),n?o.display=n:yd(t,"display"),a&&(i?r.insertBefore(t,i):r?r.appendChild(t):le.removeChild(t))),e&&6<u.length?[u[0],u[1],u[4],u[5],u[12],u[13]]:u)}function Nd(t,e,r,i,n,a){var s,o,u,h=t._gsap,l=n||Md(t,!0),f=h.xOrigin||0,d=h.yOrigin||0,p=h.xOffset||0,c=h.yOffset||0,_=l[0],m=l[1],g=l[2],v=l[3],y=l[4],b=l[5],T=e.split(" "),w=parseFloat(T[0])||0,x=parseFloat(T[1])||0;r?l!==Ge&&(o=_*v-m*g)&&(u=w*(-m/o)+x*(_/o)-(_*b-m*y)/o,w=w*(v/o)+x*(-g/o)+(g*b-v*y)/o,x=u):(w=(s=wd(t)).x+(~T[0].indexOf("%")?w/100*s.width:w),x=s.y+(~(T[1]||T[0]).indexOf("%")?x/100*s.height:x)),i||!1!==i&&h.smooth?(y=w-f,b=x-d,h.xOffset=p+(y*_+b*g)-y,h.yOffset=c+(y*m+b*v)-b):h.xOffset=h.yOffset=0,h.xOrigin=w,h.yOrigin=x,h.smooth=!!i,h.origin=e,h.originIsAbsolute=!!r,t.style[Ye]="0px 0px",a&&(zd(a,h,"xOrigin",f,w),zd(a,h,"yOrigin",d,x),zd(a,h,"xOffset",p,h.xOffset),zd(a,h,"yOffset",c,h.yOffset)),t.setAttribute("data-svg-origin",w+" "+x)}function Qd(t,e,r){var i=Qa(e);return ca(parseFloat(e)+parseFloat(Bd(t,"x",r+"px",i)))+i}function Xd(t,e,r,i,n,a){var s,u,h=360,l=o(n),f=parseFloat(n)*(l&&~n.indexOf("rad")?Ee:1),d=a?f*a:f-i,p=i+d+"deg";return l&&("short"===(s=n.split("_")[1])&&(d%=h)!==d%180&&(d+=d<0?h:-h),"cw"===s&&d<0?d=(d+36e9)%h-~~(d/h)*h:"ccw"===s&&0<d&&(d=(d-36e9)%h-~~(d/h)*h)),t._pt=u=new ae(t._pt,e,r,i,d,bd),u.e=p,u.u="deg",t._props.push(r),u}function Yd(t,e){for(var r in e)t[r]=e[r];return t}function Zd(t,e,r){var i,n,a,s,o,u,h,l=Yd({},r._gsap),f=r.style;for(n in l.svg?(a=r.getAttribute("transform"),r.setAttribute("transform",""),f[Qe]=e,i=Ze(r,1),yd(r,Qe),r.setAttribute("transform",a)):(a=getComputedStyle(r)[Qe],f[Qe]=e,i=Ze(r,1),f[Qe]=a),Fe)(a=l[n])!==(s=i[n])&&"perspective,force3D,transformOrigin,svgOrigin".indexOf(n)<0&&(o=Qa(a)!==(h=Qa(s))?Bd(r,n,a,h):parseFloat(a),u=parseFloat(s),t._pt=new ae(t._pt,i,n,o,u-o,ad),t._pt.u=h||0,t._props.push(n));Yd(i,l)}var ue,he,le,fe,de,pe,ce,_e=zt.Power0,me=zt.Power1,ge=zt.Power2,ve=zt.Power3,ye=zt.Power4,be=zt.Linear,Te=zt.Quad,we=zt.Cubic,xe=zt.Quart,Oe=zt.Quint,Me=zt.Strong,ke=zt.Elastic,Ce=zt.Back,Pe=zt.SteppedEase,Ae=zt.Bounce,Se=zt.Sine,De=zt.Expo,ze=zt.Circ,Fe={},Ee=180/Math.PI,Be=Math.PI/180,Re=Math.atan2,Ie=/([A-Z])/g,Le=/(?:left|right|width|margin|padding|x)/i,qe=/[\s,\(]\S/,Ne={autoAlpha:"opacity,visibility",scale:"scaleX,scaleY",alpha:"opacity"},Qe="transform",Ye=Qe+"Origin",je="O,Moz,ms,Ms,Webkit".split(","),Xe=function _checkPropPrefix(t,e,r){var i=(e||de).style,n=5;if(t in i&&!r)return t;for(t=t.charAt(0).toUpperCase()+t.substr(1);n--&&!(je[n]+t in i););return n<0?null:(3===n?"ms":0<=n?je[n]:"")+t},Ue={deg:1,rad:1,turn:1},Ve={top:"0%",bottom:"100%",left:"0%",right:"100%",center:"50%"},Je={clearProps:function clearProps(t,e,r,i,n){if("isFromStart"!==n.data){var a=t._pt=new ae(t._pt,e,r,0,0,Gd);return a.u=i,a.pr=-10,a.tween=n,t._props.push(r),1}}},Ge=[1,0,0,1,0,0],$e={},Ze=function _parseTransform(t,e){var r=t._gsap||new Lt(t);if("x"in r&&!e&&!r.uncache)return r;var i,n,a,s,o,u,h,l,f,d,p,c,_,m,g,v,y,b,T,w,x,O,M,k,C,P,A,S,D,z,F,E,B=t.style,R=r.scaleX<0,I="deg",L=qd(t,Ye)||"0";return i=n=a=u=h=l=f=d=p=0,s=o=1,r.svg=!(!t.getCTM||!xd(t)),m=Md(t,r.svg),r.svg&&(k=(!r.uncache||"0px 0px"===L)&&!e&&t.getAttribute("data-svg-origin"),Nd(t,k||L,!!k||r.originIsAbsolute,!1!==r.smooth,m)),c=r.xOrigin||0,_=r.yOrigin||0,m!==Ge&&(b=m[0],T=m[1],w=m[2],x=m[3],i=O=m[4],n=M=m[5],6===m.length?(s=Math.sqrt(b*b+T*T),o=Math.sqrt(x*x+w*w),u=b||T?Re(T,b)*Ee:0,(f=w||x?Re(w,x)*Ee+u:0)&&(o*=Math.abs(Math.cos(f*Be))),r.svg&&(i-=c-(c*b+_*w),n-=_-(c*T+_*x))):(E=m[6],z=m[7],A=m[8],S=m[9],D=m[10],F=m[11],i=m[12],n=m[13],a=m[14],h=(g=Re(E,D))*Ee,g&&(k=O*(v=Math.cos(-g))+A*(y=Math.sin(-g)),C=M*v+S*y,P=E*v+D*y,A=O*-y+A*v,S=M*-y+S*v,D=E*-y+D*v,F=z*-y+F*v,O=k,M=C,E=P),l=(g=Re(-w,D))*Ee,g&&(v=Math.cos(-g),F=x*(y=Math.sin(-g))+F*v,b=k=b*v-A*y,T=C=T*v-S*y,w=P=w*v-D*y),u=(g=Re(T,b))*Ee,g&&(k=b*(v=Math.cos(g))+T*(y=Math.sin(g)),C=O*v+M*y,T=T*v-b*y,M=M*v-O*y,b=k,O=C),h&&359.9<Math.abs(h)+Math.abs(u)&&(h=u=0,l=180-l),s=ca(Math.sqrt(b*b+T*T+w*w)),o=ca(Math.sqrt(M*M+E*E)),g=Re(O,M),f=2e-4<Math.abs(g)?g*Ee:0,p=F?1/(F<0?-F:F):0),r.svg&&(k=t.getAttribute("transform"),r.forceCSS=t.setAttribute("transform","")||!Kd(qd(t,Qe)),k&&t.setAttribute("transform",k))),90<Math.abs(f)&&Math.abs(f)<270&&(R?(s*=-1,f+=u<=0?180:-180,u+=u<=0?180:-180):(o*=-1,f+=f<=0?180:-180)),r.x=i-((r.xPercent=i&&(r.xPercent||(Math.round(t.offsetWidth/2)===Math.round(-i)?-50:0)))?t.offsetWidth*r.xPercent/100:0)+"px",r.y=n-((r.yPercent=n&&(r.yPercent||(Math.round(t.offsetHeight/2)===Math.round(-n)?-50:0)))?t.offsetHeight*r.yPercent/100:0)+"px",r.z=a+"px",r.scaleX=ca(s),r.scaleY=ca(o),r.rotation=ca(u)+I,r.rotationX=ca(h)+I,r.rotationY=ca(l)+I,r.skewX=f+I,r.skewY=d+I,r.transformPerspective=p+"px",(r.zOrigin=parseFloat(L.split(" ")[2])||0)&&(B[Ye]=Ke(L)),r.xOffset=r.yOffset=0,r.force3D=Y.force3D,r.renderTransform=r.svg?ir:ce?rr:He,r.uncache=0,r},Ke=function _firstTwoOnly(t){return(t=t.split(" "))[0]+" "+t[1]},He=function _renderNon3DTransforms(t,e){e.z="0px",e.rotationY=e.rotationX="0deg",e.force3D=0,rr(t,e)},We="0deg",tr="0px",er=") ",rr=function _renderCSSTransforms(t,e){var r=e||this,i=r.xPercent,n=r.yPercent,a=r.x,s=r.y,o=r.z,u=r.rotation,h=r.rotationY,l=r.rotationX,f=r.skewX,d=r.skewY,p=r.scaleX,c=r.scaleY,_=r.transformPerspective,m=r.force3D,g=r.target,v=r.zOrigin,y="",b="auto"===m&&t&&1!==t||!0===m;if(v&&(l!==We||h!==We)){var T,w=parseFloat(h)*Be,x=Math.sin(w),O=Math.cos(w);w=parseFloat(l)*Be,T=Math.cos(w),a=Qd(g,a,x*T*-v),s=Qd(g,s,-Math.sin(w)*-v),o=Qd(g,o,O*T*-v+v)}_!==tr&&(y+="perspective("+_+er),(i||n)&&(y+="translate("+i+"%, "+n+"%) "),!b&&a===tr&&s===tr&&o===tr||(y+=o!==tr||b?"translate3d("+a+", "+s+", "+o+") ":"translate("+a+", "+s+er),u!==We&&(y+="rotate("+u+er),h!==We&&(y+="rotateY("+h+er),l!==We&&(y+="rotateX("+l+er),f===We&&d===We||(y+="skew("+f+", "+d+er),1===p&&1===c||(y+="scale("+p+", "+c+er),g.style[Qe]=y||"translate(0, 0)"},ir=function _renderSVGTransforms(t,e){var r,i,n,a,s,o=e||this,u=o.xPercent,h=o.yPercent,l=o.x,f=o.y,d=o.rotation,p=o.skewX,c=o.skewY,_=o.scaleX,m=o.scaleY,g=o.target,v=o.xOrigin,y=o.yOrigin,b=o.xOffset,T=o.yOffset,w=o.forceCSS,x=parseFloat(l),O=parseFloat(f);d=parseFloat(d),p=parseFloat(p),(c=parseFloat(c))&&(p+=c=parseFloat(c),d+=c),d||p?(d*=Be,p*=Be,r=Math.cos(d)*_,i=Math.sin(d)*_,n=Math.sin(d-p)*-m,a=Math.cos(d-p)*m,p&&(c*=Be,s=Math.tan(p-c),n*=s=Math.sqrt(1+s*s),a*=s,c&&(s=Math.tan(c),r*=s=Math.sqrt(1+s*s),i*=s)),r=ca(r),i=ca(i),n=ca(n),a=ca(a)):(r=_,a=m,i=n=0),(x&&!~(l+"").indexOf("px")||O&&!~(f+"").indexOf("px"))&&(x=Bd(g,"x",l,"px"),O=Bd(g,"y",f,"px")),(v||y||b||T)&&(x=ca(x+v-(v*r+y*n)+b),O=ca(O+y-(v*i+y*a)+T)),(u||h)&&(s=g.getBBox(),x=ca(x+u/100*s.width),O=ca(O+h/100*s.height)),s="matrix("+r+","+i+","+n+","+a+","+x+","+O+")",g.setAttribute("transform",s),w&&(g.style[Qe]=s)};ba("padding,margin,Width,Radius",function(e,r){var t="Right",i="Bottom",n="Left",o=(r<3?["Top",t,i,n]:["Top"+n,"Top"+t,i+t,i+n]).map(function(t){return r<2?e+t:"border"+t+e});Je[1<r?"border"+e:e]=function(e,t,r,i,n){var a,s;if(arguments.length<4)return a=o.map(function(t){return Cd(e,t,r)}),5===(s=a.join(" ")).split(a[0]).length?a[0]:s;a=(i+"").split(" "),s={},o.forEach(function(t,e){return s[t]=a[e]=a[e]||a[(e-1)/2|0]}),e.init(t,s,n)}});var nr,ar,sr,or={name:"css",register:td,targetTest:function targetTest(t){return t.style&&t.nodeType},init:function init(t,e,r,i,n){var a,s,u,h,l,f,d,p,c,_,m,g,v,y,b,T=this._props,w=t.style,x=r.vars.startAt;for(d in fe||td(),e)if("autoRound"!==d&&(s=e[d],!ft[d]||!Tb(d,e,r,i,t,n)))if(l=typeof s,f=Je[d],"function"===l&&(l=typeof(s=s.call(r,i,t,n))),"string"===l&&~s.indexOf("random(")&&(s=gb(s)),f)f(this,t,d,s,r)&&(b=1);else if("--"===d.substr(0,2))a=(getComputedStyle(t).getPropertyValue(d)+"").trim(),s+="",Pt.lastIndex=0,Pt.test(a)||(p=Qa(a),c=Qa(s)),c?p!==c&&(a=Bd(t,d,a,c)+c):p&&(s+=p),this.add(w,"setProperty",a,s,i,n,0,0,d),T.push(d);else if("undefined"!==l){if(x&&d in x?(a="function"==typeof x[d]?x[d].call(r,i,t,n):x[d],o(a)&&~a.indexOf("random(")&&(a=gb(a)),Qa(a+"")||(a+=Y.units[d]||Qa(Cd(t,d))||""),"="===(a+"").charAt(1)&&(a=Cd(t,d))):a=Cd(t,d),h=parseFloat(a),(_="string"===l&&"="===s.charAt(1)?+(s.charAt(0)+"1"):0)&&(s=s.substr(2)),u=parseFloat(s),d in Ne&&("autoAlpha"===d&&(1===h&&"hidden"===Cd(t,"visibility")&&u&&(h=0),zd(this,w,"visibility",h?"inherit":"hidden",u?"inherit":"hidden",!u)),"scale"!==d&&"transform"!==d&&~(d=Ne[d]).indexOf(",")&&(d=d.split(",")[0])),m=d in Fe)if(g||((v=t._gsap).renderTransform&&!e.parseTransform||Ze(t,e.parseTransform),y=!1!==e.smoothOrigin&&v.smooth,(g=this._pt=new ae(this._pt,w,Qe,0,1,v.renderTransform,v,0,-1)).dep=1),"scale"===d)this._pt=new ae(this._pt,v,"scaleY",v.scaleY,(_?_*u:u-v.scaleY)||0),T.push("scaleY",d),d+="X";else{if("transformOrigin"===d){s=Fd(s),v.svg?Nd(t,s,0,y,0,this):((c=parseFloat(s.split(" ")[2])||0)!==v.zOrigin&&zd(this,v,"zOrigin",v.zOrigin,c),zd(this,w,d,Ke(a),Ke(s)));continue}if("svgOrigin"===d){Nd(t,s,1,y,0,this);continue}if(d in $e){Xd(this,v,d,h,s,_);continue}if("smoothOrigin"===d){zd(this,v,"smooth",v.smooth,s);continue}if("force3D"===d){v[d]=s;continue}if("transform"===d){Zd(this,s,t);continue}}else d in w||(d=Xe(d)||d);if(m||(u||0===u)&&(h||0===h)&&!qe.test(s)&&d in w)u=u||0,(p=(a+"").substr((h+"").length))!==(c=Qa(s)||(d in Y.units?Y.units[d]:p))&&(h=Bd(t,d,a,c)),this._pt=new ae(this._pt,m?v:w,d,h,_?_*u:u-h,m||"px"!==c&&"zIndex"!==d||!1===e.autoRound?ad:dd),this._pt.u=c||0,p!==c&&"%"!==c&&(this._pt.b=a,this._pt.r=cd);else if(d in w)Dd.call(this,t,d,a,s);else{if(!(d in t)){N(d,s);continue}this.add(t,d,a||t[d],s,i,n)}T.push(d)}b&&ne(this)},get:Cd,aliases:Ne,getSetter:function getSetter(t,e,i){var n=Ne[e];return n&&n.indexOf(",")<0&&(e=n),e in Fe&&e!==Ye&&(t._gsap.x||Cd(t,"x"))?i&&pe===i?"scale"===e?jd:id:(pe=i||{})&&("scale"===e?kd:ld):t.style&&!r(t.style[e])?gd:~e.indexOf("-")?hd:Kt(t,e)},core:{_removeProperty:yd,_getMatrix:Md}};oe.utils.checkPrefix=Xe,sr=ba((nr="x,y,z,scale,scaleX,scaleY,xPercent,yPercent")+","+(ar="rotation,rotationX,rotationY,skewX,skewY")+",transform,transformOrigin,svgOrigin,force3D,smoothOrigin,transformPerspective",function(t){Fe[t]=1}),ba(ar,function(t){Y.units[t]="deg",$e[t]=1}),Ne[sr[13]]=nr+","+ar,ba("0:translateX,1:translateY,2:translateZ,8:rotate,8:rotationZ,8:rotateZ,9:rotateX,10:rotateY",function(t){var e=t.split(":");Ne[e[1]]=sr[e[0]]}),ba("x,y,z,top,right,bottom,left,width,height,fontSize,padding,margin,perspective",function(t){Y.units[t]="px"}),oe.registerPlugin(or);var ur=oe.registerPlugin(or)||oe,hr=ur.core.Tween;e.Back=Ce,e.Bounce=Ae,e.CSSPlugin=or,e.Circ=ze,e.Cubic=we,e.Elastic=ke,e.Expo=De,e.Linear=be,e.Power0=_e,e.Power1=me,e.Power2=ge,e.Power3=ve,e.Power4=ye,e.Quad=Te,e.Quart=xe,e.Quint=Oe,e.Sine=Se,e.SteppedEase=Pe,e.Strong=Me,e.TimelineLite=Nt,e.TimelineMax=Nt,e.TweenLite=Jt,e.TweenMax=hr,e.default=ur,e.gsap=ur;if (typeof(window)==="undefined"||window!==e){Object.defineProperty(e,"__esModule",{value:!0})} else {delete e.default}});



/*
* Title Border
*/
if($('[data-title-border]').length) {

	var $pageHeaderTitleBorder = $('<span class="page-header-title-border"></span>'),
		$pageHeaderTitle = $('[data-title-border]'),
		$window = $(window);

	$pageHeaderTitle.before($pageHeaderTitleBorder);

	var setPageHeaderTitleBorderWidth = function() {
		$pageHeaderTitleBorder.width($pageHeaderTitle.width());
	}

	$window.afterResize(function(){
		setPageHeaderTitleBorderWidth();
	});

	setPageHeaderTitleBorderWidth();

	$pageHeaderTitleBorder.addClass('visible');
}

/*
* Footer Reveal
*/
(function($) {
	var $footerReveal = {
		$wrapper: $('.footer-reveal'),
		init: function() {
			var self = this;

			self.build();
			self.events();
		},
		build: function() {
			var self = this, 
				footer_height = self.$wrapper.outerHeight(true),
				window_height = ( $(window).height() - $('.header-body').height() );

			if( footer_height > window_height ) {
				$('#footer').removeClass('footer-reveal');
				$('body').css('margin-bottom', 0);
			} else {
				$('#footer').addClass('footer-reveal');
				$('body').css('margin-bottom', footer_height);
			}

		},
		events: function() {
			var self = this,
				$window = $(window);

			$window.on('load', function(){
				$window.afterResize(function(){
					self.build();
				});
			});
		}
	}

	if( $('.footer-reveal').length ) {
		$footerReveal.init();
	}
})(jQuery);

/* Re-Init Plugin */
if( $('[data-reinit-plugin]').length ) {
	$('[data-reinit-plugin]').on('click', function(e) {
		e.preventDefault();

		var pluginInstance = $(this).data('reinit-plugin'),
			pluginFunction = $(this).data('reinit-plugin-function'),
			pluginElement  = $(this).data('reinit-plugin-element'),
			pluginOptions  = theme.fn.getOptions($(this).data('reinit-plugin-options'));

		$( pluginElement ).data( pluginInstance ).destroy();

		setTimeout(function(){
			theme.fn.execPluginFunction(pluginFunction, $( pluginElement ), pluginOptions);	
		}, 1000);

	});
}

/* Simple Copy To Clipboard */
if( $('[data-copy-to-clipboard]').length ) {
	theme.fn.intObs( '[data-copy-to-clipboard]', function(){
		var $this = $(this);

		$this.wrap( '<div class="copy-to-clipboard-wrapper position-relative"></div>' );

		var $copyButton = $('<a href="#" class="btn btn-primary btn-px-2 py-1 text-0 position-absolute top-8 right-8">COPY</a>');
		$this.parent().prepend( $copyButton );

		$copyButton.on('click', function(e){
			e.preventDefault();

			var $btn       = $(this),
				$temp = $('<textarea class="d-block opacity-0" style="height: 0;">');

			$btn.parent().append( $temp );

			$temp.val( $this.text() );
				
			$temp[0].select();
			$temp[0].setSelectionRange(0, 99999);

			document.execCommand("copy");

			$btn.addClass('copied');
			setTimeout(function(){
				$btn.removeClass('copied');
			}, 1000);

			$temp.remove();
		});
	}, {
		rootMargin: '0px 0px 0px 0px'
	} );
}

// Animate
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__animate';

	var PluginAnimate = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginAnimate.defaults = {
		accX: 0,
		accY: -80,
		delay: 100,
		duration: '750ms',
		minWindowWidth: 767,
		forceAnimation: false,
		flagClassOnly: false
	};

	PluginAnimate.prototype = {
		initialize: function($el, opts) {
			if ($el.data(instanceName)) {
				return this;
			}

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginAnimate.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			var self = this;

			// Flag Class Only
			// - Useful for simple animations like hightlight
			// - Less process and memory
			if( self.options.flagClassOnly ) {
				var delay = self.options.wrapper.attr('data-appear-animation-delay') ? self.options.wrapper.attr('data-appear-animation-delay') : self.options.delay;
				
				self.options.wrapper.css({
					'animation-delay': delay + 'ms',
					'transition-delay': delay + 'ms'
				});
				self.options.wrapper.addClass( self.options.wrapper.attr('data-appear-animation') );

				return this;
			}

			if($('body').hasClass('loading-overlay-showing')) {
				$(window).on('loading.overlay.ready', function(){
					self.animate();
				});
			} else {
				self.animate();
			}

			return this;
		},

		animate: function() {
			var self = this,
				$el = this.options.wrapper,
				delay = 0,
				duration = this.options.duration,
				elTopDistance = $el.offset().top,
				windowTopDistance = $(window).scrollTop();

			// If has appear animation elements inside a SVG. 
			// Intersection Observer API do not check elements inside SVG's, so we need initialize trough top parent SVG
			if( $el.data('appear-animation-svg') ) {
				$el.find('[data-appear-animation]').each(function(){
					var $this = $(this),
						opts;

					var pluginOptions = theme.fn.getOptions($this.data('plugin-options'));
					if (pluginOptions)
						opts = pluginOptions;

					$this.themePluginAnimate(opts);	
				});

				return this;
			}

			// No animation at the first load of page. This is good for performance
			if( self.options.firstLoadNoAnim ) {
				$el.removeClass('appear-animation');

				// Inside Carousel
				if( $el.closest('.owl-carousel').get(0) ) {
					setTimeout(function(){
						$el.closest('.owl-carousel').on('change.owl.carousel', function(){
							self.options.firstLoadNoAnim = false;
							$el.removeData('__animate');
							$el.themePluginAnimate( self.options );
						});
					}, 500);
				}

				return this;
			}

			$el.addClass('appear-animation animated');

			if (!$('html').hasClass('no-csstransitions') && $(window).width() > self.options.minWindowWidth && elTopDistance >= windowTopDistance || self.options.forceAnimation == true) {
				delay = ($el.attr('data-appear-animation-delay') ? $el.attr('data-appear-animation-delay') : self.options.delay);
				duration = ($el.attr('data-appear-animation-duration') ? $el.attr('data-appear-animation-duration') : self.options.duration);

				if (duration != '750ms') {
					$el.css('animation-duration', duration);
				}

				$el.css('animation-delay', delay + 'ms');
				$el.addClass($el.attr('data-appear-animation') + ' appear-animation-visible');
				
				$el.trigger('animation:show');

			} else {
				$el.addClass('appear-animation-visible');
			}

			return this;
		}
	};

	// expose to scope
	$.extend(theme, {
		PluginAnimate: PluginAnimate
	});

	// jquery plugin
	$.fn.themePluginAnimate = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginAnimate($this, opts);
			}

		});
	};

}).apply(this, [window.theme, jQuery]);

// Animated Letters
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__animatedLetters';

	var PluginAnimatedLetters = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginAnimatedLetters.defaults = {
		animationName: 'fadeIn',
		animationSpeed: 50,
		startDelay: 500,
		minWindowWidth: 768,
		letterClass: ''
	};

	PluginAnimatedLetters.prototype = {
		initialize: function($el, opts) {
			if ($el.data(instanceName)) {
				return this;
			}

			var self = this;

			this.$el = $el;
			this.initialText = $el.text();

			this
				.setData()
				.setOptions(opts)
				.build()
				.events();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginAnimatedLetters.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			var self    = this,
				letters = self.$el.text().split('');

			if( $(window).width() < self.options.minWindowWidth ) {
				return this;
			}

			if( self.options.firstLoadNoAnim ) {
				self.$el.css({
					visibility: 'visible'
				});

				// Inside Carousel
				if( self.$el.closest('.owl-carousel').get(0) ) {
					setTimeout(function(){
						self.$el.closest('.owl-carousel').on('change.owl.carousel', function(){
							self.options.firstLoadNoAnim = false;
							self.build();
						});
					}, 500);
				}

				return this;
			}

			// Add class to show
			self.$el.addClass('initialized');			

			// Set Min Height to avoid flicking issues
			self.setMinHeight();

			self.$el.text('');

			if( self.options.animationName == 'typeWriter' ) {
				self.$el.append( '<span class="letters-wrapper"></span><span class="typeWriter"></pre>' );

				var index = 0;

				setTimeout(function(){

					var timeout = function(){
						var st = setTimeout(function(){
							var letter = letters[index];
							
							self.$el.find('.letters-wrapper').append( '<span class="letter '+ ( self.options.letterClass ? self.options.letterClass + ' ' : '' ) +'">' + letter + '</span>' );

							index++;
							timeout();
						}, self.options.animationSpeed);

						if( index >= letters.length ) {
							clearTimeout(st);
						}
					};
					timeout();

				}, self.options.startDelay);
			} else {
				setTimeout(function(){
					for( var i = 0; i < letters.length; i++ ) {
						var letter = letters[i];
						
						self.$el.append( '<span class="letter '+ ( self.options.letterClass ? self.options.letterClass + ' ' : '' ) + self.options.animationName +' animated" style="animation-delay: '+ ( i * self.options.animationSpeed ) +'ms;">' + letter + '</span>' );
	
					}
				}, self.options.startDelay);
			}

			return this;
		},

		setMinHeight: function() {
			var self = this;

			// if it's inside carousel
			if( self.$el.closest('.owl-carousel').get(0) ) {
				self.$el.closest('.owl-carousel').addClass('d-block');
				self.$el.css( 'min-height', self.$el.height() );
				self.$el.closest('.owl-carousel').removeClass('d-block');
			} else {
				self.$el.css( 'min-height', self.$el.height() );
			}

			return this;
		},

		destroy: function() {
			var self = this;

			self.$el
				.html( self.initialText )
				.css( 'min-height', '' );

			return this;
		},

		events: function() {
			var self = this;

			// Destroy
			self.$el.on('animated.letters.destroy', function(){
				self.destroy();
			});

			// Initialize
			self.$el.on('animated.letters.initialize', function(){
				self.build();
			});

			return this;
		}
	};

	// expose to scope
	$.extend(theme, {
		PluginAnimatedLetters: PluginAnimatedLetters
	});

	// jquery plugin
	$.fn.themePluginAnimatedLetters = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginAnimatedLetters($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// Before / After
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__beforeafter';

	var PluginBeforeAfter = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginBeforeAfter.defaults = {
		default_offset_pct: 0.5,
		orientation: 'horizontal',
		before_label: 'Before',
		after_label: 'After',
		no_overlay: false,
		move_slider_on_hover: false,
		move_with_handle_only: true,
		click_to_move: false
	};

	PluginBeforeAfter.prototype = {
		initialize: function($el, opts) {
			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginBeforeAfter.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {

			if (!($.isFunction($.fn.twentytwenty))) {
				return this;
			}

			var self = this;

			self.options.wrapper
				.twentytwenty(self.options);

			return this;

		}
	};

	// expose to scope
	$.extend(theme, {
		PluginBeforeAfter: PluginBeforeAfter
	});

	// jquery plugin
	$.fn.themePluginBeforeAfter = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginBeforeAfter($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// Carousel Light
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__carouselLight';

	var PluginCarouselLight = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginCarouselLight.defaults = {
		autoplay: true,
		autoplayTimeout: 7000,
		disableAutoPlayOnClick: true
	};

	PluginCarouselLight.prototype = {
		initialize: function($el, opts) {
			if ($el.data(instanceName)) {
				return this;
			}

			this.$el = $el;
			this.clickFlag = true;

			this
				.setData()
				.setOptions(opts)
				.build()
				.owlNav()
				.owlDots()
				.autoPlay()
				.events();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginCarouselLight.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			var self = this;

			self.$el
				.css('opacity', 1)
				.find('.owl-item:first-child')
				.addClass('active');

			self.$el.trigger('initialized.owl.carousel');

			// Carousel Navigate By ID and item index
			self.carouselNavigate();

			return this;
		},

		changeSlide: function( $nextSlide ) {
			var self = this,
				$prevSlide = self.$el.find('.owl-item.active');

			self.$el.find('.owl-item.active').addClass('removing');

			$prevSlide
				.removeClass('fadeIn')
				.addClass( 'fadeOut animated' );

			setTimeout(function(){
				setTimeout(function(){
					$prevSlide.removeClass('active');
				}, 400);

				$nextSlide
					.addClass('active')
					.removeClass('fadeOut')
					.addClass( 'fadeIn animated' );

			}, 200);

			// Dots
			self.$el
				.find('.owl-dot')
				.removeClass('active')
				.eq( $nextSlide.index() )
				.addClass('active');

			self.$el.trigger({
				type: 'change.owl.carousel',
				nextSlideIndex: $nextSlide.index(),
				prevSlideIndex: $prevSlide.index()
			});

			setTimeout(function(){
				self.$el.trigger({
					type: 'changed.owl.carousel',
					nextSlideIndex: $nextSlide.index(),
					prevSlideIndex: $prevSlide.index()
				});
			}, 500);
		},

		owlNav: function() {
			var self = this,
				$owlNext = self.$el.find('.owl-next'),
				$owlPrev = self.$el.find('.owl-prev');

			$owlPrev.on('click', function(e){
				e.preventDefault();

				if( self.options.disableAutoPlayOnClick ) {
					window.clearInterval(self.autoPlayInterval);
				}

				if( self.avoidMultipleClicks() ) {
					return false;
				}

				self.owlPrev();
			});

			$owlNext.on('click', function(e){
				e.preventDefault();

				if( self.options.disableAutoPlayOnClick ) {
					window.clearInterval(self.autoPlayInterval);
				}

				if( self.avoidMultipleClicks() ) {
					return false;
				}

				self.owlNext();
			});

			return this;
		},

		owlDots: function(){
			var self = this,
				$owlDot = self.$el.find('.owl-dot');

			$owlDot.on('click', function(e){
				$this = $(this);

				e.preventDefault();

				if( self.options.disableAutoPlayOnClick ) {
					window.clearInterval(self.autoPlayInterval);
				}

				if( self.avoidMultipleClicks() ) {
					return false;
				}

				var dotIndex = $(this).index();

				// Do nothing if respective dot slide is active/showing
				if( $this.hasClass('active') ) {
					return false;
				}

				self.changeSlide( self.$el.find('.owl-item').eq( dotIndex ) );
			});

			return this;
		},

		owlPrev: function() {
			var self = this;

			if( self.$el.find('.owl-item.active').prev().get(0) ) {
				self.changeSlide( self.$el.find('.owl-item.active').prev() );
			} else {
				self.changeSlide( self.$el.find('.owl-item:last-child') );
			}
		},

		owlNext: function() {
			var self = this;

			if( self.$el.find('.owl-item.active').next().get(0) ) {
				self.changeSlide( self.$el.find('.owl-item.active').next() );
			} else {
				self.changeSlide( self.$el.find('.owl-item').eq(0) );
			}
		},

		avoidMultipleClicks: function() {
			var self = this;

			if( !self.clickFlag ) {
				return true;
			}

			if( self.clickFlag ) {
				self.clickFlag = false;
				setTimeout(function(){
					self.clickFlag = true; 
				}, 1000);
			}

			return false;
		},

		autoPlay: function(){
			var self = this,
				$el  = this.options.wrapper;

			if( self.options.autoplay ) {
				self.autoPlayInterval = window.setInterval(function() {
					self.owlNext();
				}, self.options.autoplayTimeout);
			}

			return this;
		},

		carouselNavigate: function() {
			var self      = this,
				$el       = this.options.wrapper,
				$carousel = $el;

			if( $('[data-carousel-navigate]').get(0) ) {
				$('[data-carousel-navigate-id="#'+ $el.attr('id') +'"]').each(function(){
					var $this = $(this),
						hasCarousel = $( $this.data('carousel-navigate-id') ).get(0),
						toIndex = $this.data('carousel-navigate-to');

					if( hasCarousel ) {

						$this.on('click', function(){

							if( self.options.disableAutoPlayOnClick ) {
								window.clearInterval(self.autoPlayInterval);
							}
							
							self.changeSlide( self.$el.find('.owl-item').eq( parseInt(toIndex) - 1 ) );
						});

					}
				});

				$el.on('change.owl.carousel', function(e){
					$('[data-carousel-navigate-id="#'+ $el.attr('id') +'"]').removeClass('active');
				});

				$el.on('changed.owl.carousel', function(e){
					$('[data-carousel-navigate-id="#'+ $el.attr('id') +'"][data-carousel-navigate-to="'+ ( e.nextSlideIndex + 1 ) +'"]').addClass('active');
				});
			}

			return this;
		},

		events: function() {
			var self = this;

			self.$el.on('change.owl.carousel', function(event) {

				// Hide elements inside carousel
			    self.$el.find('[data-appear-animation]:not(.background-image-wrapper), [data-plugin-animated-letters]').addClass('invisible');

			    // Animated Letters
			    self.$el.find('[data-plugin-animated-letters]').trigger('animated.letters.destroy');

			    // Remove "d-none" class before show the element. This is useful when using background images inside a carousel. Like ken burns effect
			    self.$el.find('.owl-item:not(.active) [data-carousel-onchange-show]').removeClass('d-none');

			});

			self.$el.on('changed.owl.carousel', function(event) {
				setTimeout(function(){

				    // Appear Animation
				    if( self.$el.find('.owl-item.cloned [data-appear-animation]').get(0) ) {
				    	self.$el.find('.owl-item.cloned [data-appear-animation]').each(function() {
							var $this = $(this),
								opts;

							var pluginOptions = theme.fn.getOptions($this.data('plugin-options'));
							if (pluginOptions)
								opts = pluginOptions;

							$this.themePluginAnimate(opts);
						});
				    }

					// Show elements inside carousel
				    self.$el.find('.owl-item.active [data-appear-animation]:not(.background-image-wrapper), [data-plugin-animated-letters]').removeClass('invisible');

				    // Animated Letters
				    self.$el.find('.owl-item.active [data-plugin-animated-letters]').trigger('animated.letters.initialize');

				    // Background Video
				    self.$el.find('.owl-item.cloned.active [data-plugin-video-background]').trigger('video.background.initialize');

				}, 500);
			    
			});
		}
	};

	// expose to scope
	$.extend(theme, {
		PluginCarouselLight: PluginCarouselLight
	});

	// jquery plugin
	$.fn.themePluginCarouselLight = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginCarouselLight($this, opts);
			}

		});
	};

}).apply(this, [window.theme, jQuery]);

// Carousel
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__carousel';

	var PluginCarousel = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginCarousel.defaults = {
		loop: true,
		responsive: {
			0: {
				items: 1
			},
			479: {
				items: 1
			},
			768: {
				items: 2
			},
			979: {
				items: 3
			},
			1199: {
				items: 4
			}
		},
		navText: [],
		refresh: false
	};

	PluginCarousel.prototype = {
		initialize: function($el, opts) {
			if ($el.data(instanceName)) {
				return this;
			}

			this.$el = $el;

			// If has data-icon inside, initialize only after icons get rendered
			// Prevent flicking issues
			if( $el.find('[data-icon]').get(0) ) {
				var self = this;

				$(window).on('icon.rendered', function(){
					if ($el.data(instanceName)) {
						return this;
					}

					setTimeout(function(){
						self
							.setData()
							.setOptions(opts)
							.build();
					}, 1000);
				});

				return this;
			}

			this
				.setData()
				.setOptions(opts)
				.build();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginCarousel.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			if (!($.isFunction($.fn.owlCarousel))) {
				return this;
			}

			var self = this,
				$el = this.options.wrapper;

			// Add Theme Class
			$el.addClass('owl-theme');

			// Add Loading
			$el.addClass('owl-loading');

			// Force RTL according to HTML dir attribute
			if ($('html').attr('dir') == 'rtl') {
				this.options = $.extend(true, {}, this.options, {
					rtl: true
				});
			}

			if (this.options.items == 1) {
				this.options.responsive = {}
			}

			if (this.options.items > 4) {
				this.options = $.extend(true, {}, this.options, {
					responsive: {
						1199: {
							items: this.options.items
						}
					}
				});
			}

			// Auto Height Fixes
			if (this.options.autoHeight) {
				var itemsHeight = [];

				$el.find('.owl-item').each(function(){
					if( $(this).hasClass('active') ) {
						itemsHeight.push( $(this).height() );
					}
				});

				$(window).afterResize(function() {
					$el.find('.owl-stage-outer').height( Math.max.apply(null, itemsHeight) );
				});

				$(window).on('load', function() {
					$el.find('.owl-stage-outer').height( Math.max.apply(null, itemsHeight) );
				});
			}

			// Initialize OwlCarousel
			$el.owlCarousel(this.options).addClass('owl-carousel-init animated fadeIn');

			// Remove "animated fadeIn" class to prevent conflicts
			setTimeout(function(){
				$el.removeClass('animated fadeIn');
			}, 1000);

			// Owl Carousel Wrapper
			if( $el.closest('.owl-carousel-wrapper').get(0) ) {
				setTimeout(function(){
					$el.closest('.owl-carousel-wrapper').css({
						height: ''
					});
				}, 500);
			}

			// Owl Carousel Loader
			if( $el.prev().hasClass('owl-carousel-loader') ) {
				$el.prev().remove();
			}

			// Nav Offset
			self.navigationOffsets();

			// Nav Outside
			if( $el.hasClass('nav-outside') ) {
				$(window).on('owl.carousel.nav.outside', function(){
					if( $(window).width() < 992 ) {
						self.options.stagePadding = 40;
						$el.addClass('stage-margin');
					} else {
						self.options.stagePadding = 0;
						$el.removeClass('stage-margin');
					}

					$el.owlCarousel('destroy').owlCarousel( self.options );

					// Nav Offset
					self.navigationOffsets();
				});

				// Window Resize
				$(window).on('load', function(){
					$(window).afterResize(function(){
						$(window).trigger('owl.carousel.nav.outside');
					});
				});

				// First Load
				$(window).trigger('owl.carousel.nav.outside');
			}

			// Nav style 5 (SVG Arrows)
			if( $el.hasClass('nav-svg-arrows-1') ) {
				var svg_arrow = '' +
					'<svg version="1.1" viewBox="0 0 15.698 8.706" width="17" xml:space="preserve" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">' +
						'<polygon stroke="#212121" stroke-width="0.1" fill="#212121" points="11.354,0 10.646,0.706 13.786,3.853 0,3.853 0,4.853 13.786,4.853 10.646,8 11.354,8.706 15.698,4.353 "/>' +
					'</svg>';

				$el.find('.owl-next, .owl-prev').append( svg_arrow );
			}

			// Sync
			if( $el.attr('data-sync') ) {
				$el.on('change.owl.carousel', function(event) {
					if (event.namespace && event.property.name === 'position') {
					    var target = event.relatedTarget.relative(event.property.value, true);
					    $( $el.data('sync') ).owlCarousel('to', target, 300, true);				        
				  	}
				});
			}

			// Carousel Center Active Item
			if( $el.hasClass('carousel-center-active-item') ) {
				var itemsActive    = $el.find('.owl-item.active'),
					indexCenter    = Math.floor( ($el.find('.owl-item.active').length - 1) / 2 ),
					itemCenter     = itemsActive.eq(indexCenter);

				itemCenter.addClass('current');

				$el.on('change.owl.carousel', function(event) {
				  	$el.find('.owl-item').removeClass('current');
					
					setTimeout(function(){
					  	var itemsActive    = $el.find('.owl-item.active'),
					  		indexCenter    = Math.floor( ($el.find('.owl-item.active').length - 1) / 2 ),
					  		itemCenter     = itemsActive.eq(indexCenter);

					  	itemCenter.addClass('current');
					}, 100);
				});

				// Refresh
				$el.trigger('refresh.owl.carousel');

			}

			// AnimateIn / AnimateOut Fix
			if( self.options.animateIn || self.options.animateOut ) {
				$el.on('change.owl.carousel', function(event) {

					// Hide elements inside carousel
				    $el.find('[data-appear-animation], [data-plugin-animated-letters]').addClass('d-none');

				    // Animated Letters
				    $el.find('[data-plugin-animated-letters]').trigger('animated.letters.destroy');

				    // Remove "d-none" class before show the element. This is useful when using background images inside a carousel. Like ken burns effect
				    $el.find('.owl-item:not(.active) [data-carousel-onchange-show]').removeClass('d-none');

				});

				$el.on('changed.owl.carousel', function(event) {
					setTimeout(function(){

					    // Appear Animation
				    	$el.find('[data-appear-animation]').each(function() {
							var $this = $(this),
								opts;

							var pluginOptions = theme.fn.getOptions($this.data('plugin-options'));
							if (pluginOptions)
								opts = pluginOptions;

							$this.themePluginAnimate(opts);
						});

						// Show elements inside carousel
					    $el.find('.owl-item.active [data-appear-animation], [data-plugin-animated-letters]').removeClass('d-none');

					    // Animated Letters
					    $el.find('.owl-item.active [data-plugin-animated-letters]').trigger('animated.letters.initialize');

					    // Background Video
					    $el.find('.owl-item.cloned.active [data-plugin-video-background]').trigger('video.background.initialize');

					}, 10);
				    
				});
			}

			// data-icon inside carousel
			if( $el.find('[data-icon]').length ) {
				$el.on('change.owl.carousel drag.owl.carousel', function(){
					$el.find('.owl-item.cloned [data-icon]').each(function(){
						var $this = $(this),
							opts;

						var pluginOptions = theme.fn.getOptions($this.data('plugin-options'));
						if (pluginOptions)
							opts = pluginOptions;

						$this.themePluginIcon(opts);	
					});
				});
			}

			// Render Background Videos inside carousel. Just a trigger on window is sufficient to render
			if( $el.find('[data-plugin-video-background]').get(0) ) {
				$(window).resize();
			}

			// Remove Loading
			$el.removeClass('owl-loading');

			// Remove Height
			$el.css('height', 'auto');

			// Carousel Navigate By ID and item index
			self.carouselNavigate();

			// Refresh Carousel
			if( self.options.refresh ) {
				$el.owlCarousel('refresh');
			}

			return this;
		},

		navigationOffsets: function() {
			var self 			 = this,
				$el  			 = this.options.wrapper,
				navHasTransform  = $el.find('.owl-nav').css('transform') == 'none' ? false : true,
				dotsHasTransform = $el.find('.owl-dots').css('transform') == 'none' ? false : true;

			// ************* NAV *****************
			// Nav Offset - Horizontal
			if( self.options.navHorizontalOffset && !self.options.navVerticalOffset ) {
				if( !navHasTransform ) {
					$el.find('.owl-nav').css({
						transform: 'translate3d('+ self.options.navHorizontalOffset +', 0, 0)'
					});
				} else {
					$el.find('.owl-nav').css({
						left: self.options.navHorizontalOffset
					});
				}
			}

			// Nav Offset - Vertical
			if( self.options.navVerticalOffset && !self.options.navHorizontalOffset ) {
				if( !navHasTransform ) {
					$el.find('.owl-nav').css({
						transform: 'translate3d(0, '+ self.options.navVerticalOffset +', 0)'
					});
				} else {
					$el.find('.owl-nav').css({
						top: 'calc( 50% - '+ self.options.navVerticalOffset +' )'
					});
				}
			}

			// Nav Offset - Horizontal & Vertical
			if( self.options.navVerticalOffset && self.options.navHorizontalOffset ) {
				if( !navHasTransform ) {
					$el.find('.owl-nav').css({
						transform: 'translate3d('+ self.options.navHorizontalOffset +', '+ self.options.navVerticalOffset +', 0)'
					});
				} else {
					$el.find('.owl-nav').css({
						top: 'calc( 50% - '+ self.options.navVerticalOffset +' )',
						left: self.options.navHorizontalOffset
					});
				}
			}

			// ********** DOTS *********************
			// Dots Offset - Horizontal
			if( self.options.dotsHorizontalOffset && !self.options.dotsVerticalOffset ) {
				$el.find('.owl-dots').css({
					transform: 'translate3d('+ self.options.dotsHorizontalOffset +', 0, 0)'
				});
			}

			// Dots Offset - Vertical
			if( self.options.dotsVerticalOffset && !self.options.dotsHorizontalOffset ) {
				if( !dotsHasTransform ) {
					$el.find('.owl-dots').css({
						transform: 'translate3d(0, '+ self.options.dotsVerticalOffset +', 0)'
					});
				} else {
					$el.find('.owl-dots').css({
						top: 'calc( 50% - '+ self.options.dotsVerticalOffset +' )'
					});
				}
			}

			// Dots Offset - Horizontal & Vertical
			if( self.options.dotsVerticalOffset && self.options.dotsHorizontalOffset ) {
				$el.find('.owl-dots').css({
					transform: 'translate3d('+ self.options.dotsHorizontalOffset +', '+ self.options.dotsVerticalOffset +', 0)'
				});
			}

			return this;
		},

		carouselNavigate: function() {
			var self      = this,
				$el       = this.options.wrapper,
				$carousel = $el.data('owl.carousel');

			if( $('[data-carousel-navigate]').get(0) ) {
				$('[data-carousel-navigate-id="#'+ $el.attr('id') +'"]').each(function(){
					var $this = $(this),
						hasCarousel = $( $this.data('carousel-navigate-id') ).get(0),
						toIndex = $this.data('carousel-navigate-to');

					if( hasCarousel ) {

						$this.on('click', function(){
							$carousel.to( parseInt(toIndex) - 1 );
						});

					}
				});

				$el.on('change.owl.carousel', function(){
					$('[data-carousel-navigate-id="#'+ $el.attr('id') +'"]').removeClass('active');
				});

				$el.on('changed.owl.carousel', function(e){
					$('[data-carousel-navigate-id="#'+ $el.attr('id') +'"][data-carousel-navigate-to="'+ ( e.item.index + 1 ) +'"]').addClass('active');
				});
			}

			return this;
		}
	};

	// expose to scope
	$.extend(theme, {
		PluginCarousel: PluginCarousel
	});

	// jquery plugin
	$.fn.themePluginCarousel = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginCarousel($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// Chart Circular
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__chartCircular';

	var PluginChartCircular = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginChartCircular.defaults = {
		accX: 0,
		accY: -150,
		delay: 1,
		barColor: '#0088CC',
		trackColor: '#f2f2f2',
		scaleColor: false,
		scaleLength: 5,
		lineCap: 'round',
		lineWidth: 13,
		size: 175,
		rotate: 0,
		animate: ({
			duration: 2500,
			enabled: true
		})
	};

	PluginChartCircular.prototype = {
		initialize: function($el, opts) {
			if ($el.data(instanceName)) {
				return this;
			}

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginChartCircular.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			if (!($.isFunction($.fn.easyPieChart))) {
				return this;
			}

			var self = this,
				$el = this.options.wrapper,
				value = ($el.attr('data-percent') ? $el.attr('data-percent') : 0),
				percentEl = $el.find('.percent');

			$.extend(true, self.options, {
				onStep: function(from, to, currentValue) {
					percentEl.html(parseInt(currentValue));
				}
			});

			$el.attr('data-percent', 0);

			$el.easyPieChart(self.options);

			setTimeout(function() {

				$el.data('easyPieChart').update(value);
				$el.attr('data-percent', value);

			}, self.options.delay);

			return this;
		}
	};

	// expose to scope
	$.extend(theme, {
		PluginChartCircular: PluginChartCircular
	});

	// jquery plugin
	$.fn.themePluginChartCircular = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginChartCircular($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// Countdown
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__countdown';

	var PluginCountdown = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginCountdown.defaults = {
		date: '2030/06/10 12:00:00',
		textDay: 'DAYS',
		textHour: 'HRS',
		textMin: 'MIN',
		textSec: 'SEC',
		uppercase: true,
		numberClass: '',
		wrapperClass: '',
		insertHTMLbefore: '',
		insertHTMLafter: ''
	};

	PluginCountdown.prototype = {
		initialize: function($el, opts) {
			if ($el.data(instanceName)) {
				return this;
			}

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginCountdown.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			if (!($.isFunction($.fn.countTo))) {
				return this;
			}

			var self = this,
				$el = this.options.wrapper,
				numberClass = ( self.options.numberClass ) ? ' ' + self.options.numberClass : '',
				wrapperClass = ( self.options.wrapperClass ) ? ' ' + self.options.wrapperClass : '';

			if( self.options.uppercase ) {
				$el.countdown(self.options.date).on('update.countdown', function(event) {
					var $this = $(this).html(event.strftime(self.options.insertHTMLbefore
						+ '<span class="days'+ wrapperClass +'"><span class="'+ numberClass +'">%D</span> '+ self.options.textDay +'</span> '
						+ '<span class="hours'+ wrapperClass +'"><span class="'+ numberClass +'">%H</span> '+ self.options.textHour +'</span> '
						+ '<span class="minutes'+ wrapperClass +'"><span class="'+ numberClass +'">%M</span> '+ self.options.textMin +'</span> '
						+ '<span class="seconds'+ wrapperClass +'"><span class="'+ numberClass +'">%S</span> '+ self.options.textSec +'</span> '
						+ self.options.insertHTMLafter
					));
				});
			} else {
				$el.countdown(self.options.date).on('update.countdown', function(event) {
					var $this = $(this).html(event.strftime(self.options.insertHTMLbefore
						+ '<span class="days'+ wrapperClass +'"><span class="'+ numberClass +'">%D</span> '+ self.options.textDay +'</span> '
						+ '<span class="hours'+ wrapperClass +'"><span class="'+ numberClass +'">%H</span> '+ self.options.textHour +'</span> '
						+ '<span class="minutes'+ wrapperClass +'"><span class="'+ numberClass +'">%M</span> '+ self.options.textMin +'</span> '
						+ '<span class="seconds'+ wrapperClass +'"><span class="'+ numberClass +'">%S</span> '+ self.options.textSec +'</span> '
						+ self.options.insertHTMLafter
					));
				});
			}

			return this;
		}
	};

	// expose to scope
	$.extend(theme, {
		PluginCountdown: PluginCountdown
	});

	// jquery plugin
	$.fn.themePluginCountdown = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginCountdown($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// Counter
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__counter';

	var PluginCounter = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginCounter.defaults = {
		accX: 0,
		accY: 0,
		appendWrapper: false,
		prependWrapper: false,
		speed: 3000,
		refreshInterval: 100,
		decimals: 0,
		onUpdate: null,
		onComplete: null
	}

	PluginCounter.prototype = {
		initialize: function($el, opts) {
			if ($el.data(instanceName)) {
				return this;
			}

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginCounter.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			if (!($.isFunction($.fn.countTo))) {
				return this;
			}

			var self = this,
				$el = this.options.wrapper;

			$.extend(self.options, {
				onComplete: function() {
					
					if ($el.data('append')) {
						if( self.options.appendWrapper ) {
							var appendWrapper = $( self.options.appendWrapper );

							appendWrapper.append( $el.data('append') );

							$el.html( $el.html() + appendWrapper[0].outerHTML );
						} else {
							$el.html($el.html() + $el.data('append'));
						}
					}

					if ($el.data('prepend')) {
						if( self.options.prependWrapper ) {
							var prependWrapper = $( self.options.prependWrapper );

							prependWrapper.append( $el.data('prepend') );

							$el.html( $el.html() + prependWrapper[0].outerHTML );
						} else {
							$el.html($el.data('prepend') + $el.html());
						}
					}
				}
			});

			$el.countTo(self.options);

			return this;
		}
	};

	// expose to scope
	$.extend(theme, {
		PluginCounter: PluginCounter
	});

	// jquery plugin
	$.fn.themePluginCounter = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginCounter($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// CursorEffect
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__cursorEffect';

	var PluginCursorEffect = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginCursorEffect.defaults = {

	}

	PluginCursorEffect.prototype = {
		initialize: function($el, opts) {
			if ($el.data(instanceName)) {
				return this;
			}

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build()
				.events();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginCursorEffect.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			var self = this;

			// Global Variables for cursor position
			self.clientX = -100;
			self.clientY = -100;

			// Hide Mouse Cursor
			if( self.options.hideMouseCursor ) {
				self.$el.addClass('hide-mouse-cursor');
			}

			// Creates the cursor wrapper node
			var cursorOuter = document.createElement('DIV');
				cursorOuter.className = 'cursor-outer';

			// Creates the cursor inner node
			var cursorInner = document.createElement('DIV');
				cursorInner.className = 'cursor-inner';

			// Custom Cursor Outer Color
			if( self.options.cursorOuterColor ) {
				cursorOuter.style = 'border-color: ' + self.options.cursorOuterColor + ';';
			}

			// Custom Cursor Inner Color
			if( self.options.cursorInnerColor ) {
				cursorInner.style = 'background-color: ' + self.options.cursorInnerColor + ';';
			}

			// Size
			if( self.options.size ) {
				switch ( self.options.size ) {
					case 'small':
						self.$el.addClass( 'cursor-effect-size-small' );
						break;
					
					case 'big':
						self.$el.addClass( 'cursor-effect-size-big' );
						break;
				}
			}

			// Style
			if( self.options.style ) {
				self.$el.addClass( self.options.style );
			}

			// Prepend cursor wrapper node to the body
			document.body.prepend( cursorOuter );

			// Prepend cursor inner node to the body
			document.body.prepend( cursorInner );

			// Loop for render
			var render = function() {
				cursorOuter.style.transform = 'translate('+ self.clientX +'px, '+ self.clientY +'px)';
				cursorInner.style.transform = 'translate('+ self.clientX +'px, '+ self.clientY +'px)';

				self.loopInside = requestAnimationFrame(render);
			}
			self.loop = requestAnimationFrame(render);

			return this;
		},

		events: function() {
			var self = this,
				$cursorOuter = $('.cursor-outer'),
				$cursorInner = $('.cursor-inner');

			var initialCursorOuterBox    = $cursorOuter[0].getBoundingClientRect(),
				initialCursorOuterRadius = $cursorOuter.css('border-radius');

			// Update Cursor Position
			document.addEventListener('mousemove', function(e){
				if( !self.isStuck ) {
					self.clientX = e.clientX - 20;
					self.clientY = e.clientY - 20;
				}

				$cursorOuter.removeClass('opacity-0');
			});

			self.isStuck = false;
			$('[data-cursor-effect-hover]').on('mouseenter', function(e){

				// Identify Event With Hover Class
				$cursorOuter.addClass('cursor-outer-hover');
				$cursorInner.addClass('cursor-inner-hover');

				// Hover Color
				var hoverColor = $(this).data('cursor-effect-hover-color');
				$cursorOuter.addClass( 'cursor-color-' + hoverColor );
				$cursorInner.addClass( 'cursor-color-' + hoverColor );

				// Effect Types
				switch ( $(this).data('cursor-effect-hover') ) {
					case 'fit':
						var thisBox = $(this)[0].getBoundingClientRect();

						self.clientX = thisBox.x;
						self.clientY = thisBox.y;

						$cursorOuter.css({
							width: thisBox.width,
							height: thisBox.height,
							'border-radius': $(this).css('border-radius')
						}).addClass('cursor-outer-fit');

						$cursorInner.addClass('opacity-0');

						self.isStuck = true;
						break;

					case 'plus':
						$cursorInner.addClass('cursor-inner-plus');
						break;
				}
			});

			$('[data-cursor-effect-hover]').on('mouseleave', function(){
				
				// Identify Event With Hover Class
				$cursorOuter.removeClass('cursor-outer-hover');
				$cursorInner.removeClass('cursor-inner-hover');

				// Remove Color Class
				var hoverColor = $(this).data('cursor-effect-hover-color');
				$cursorOuter.removeClass( 'cursor-color-' + hoverColor );
				$cursorInner.removeClass( 'cursor-color-' + hoverColor );

				// Effect Types
				switch ( $(this).data('cursor-effect-hover') ) {
					case 'fit':
						$cursorOuter.css({
							width: initialCursorOuterBox.width,
							height: initialCursorOuterBox.height,
							'border-radius': initialCursorOuterRadius
						}).removeClass('cursor-outer-fit');

						$cursorInner.removeClass('opacity-0');

						self.isStuck = false;
						break;

					case 'plus':
						$cursorInner.removeClass('cursor-inner-plus');
						break;
				}
			});

			$(window).on('scroll', function(){
				if( $cursorOuter.hasClass('cursor-outer-fit') ) {
					$cursorOuter.addClass('opacity-0').removeClass('cursor-outer-fit');
				}
			});

			return this;
		},

		destroy: function() {
			var self = this;

			self.$el.removeClass('hide-mouse-cursor cursor-effect-size-small cursor-effect-size-big cursor-effect-style-square');		

			cancelAnimationFrame( self.loop );
			cancelAnimationFrame( self.loopInside );

			document.querySelector('.cursor-outer').remove();
			document.querySelector('.cursor-inner').remove();

			self.$el.removeData( instanceName, self );
		}
	};

	// expose to scope
	$.extend(theme, {
		PluginCursorEffect: PluginCursorEffect
	});

	// jquery plugin
	$.fn.themePluginCursorEffect = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginCursorEffect($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// Float Element
(function(theme, $) {

	'use strict';

	theme = theme || {};

	var instanceName = '__floatElement';

	var PluginFloatElement = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginFloatElement.defaults = {
		startPos: 'top',
		speed: 3,
		horizontal: false,
		isInsideSVG: false,
		transition: false,
		transitionDelay: 0,
		transitionDuration: 500
	};

	PluginFloatElement.prototype = {
		initialize: function($el, opts) {
			if ($el.data(instanceName)) {
				return this;
			}

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginFloatElement.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			var self = this,
				$el = this.options.wrapper,
				$window = $(window),
				minus;

			// If has floating elements inside a SVG. 
			// Intersection Observer API do not check elements inside SVG's, so we need initialize trough top parent SVG
			if( $el.data('plugin-float-element-svg') ) {
				$el.find('[data-plugin-float-element]').each(function(){
					var $this = $(this),
						opts;

					var pluginOptions = theme.fn.getOptions($this.data('plugin-options'));
					if (pluginOptions)
						opts = pluginOptions;

					$this.themePluginFloatElement(opts);
				});

				return this;
			}

			if( self.options.style ) {
				$el.attr('style', self.options.style);
			}

			if( $window.width() > 767 ) {

				// Set Start Position
				if( self.options.startPos == 'none' ) {
					minus = '';
				} else if( self.options.startPos == 'top' ) {
					$el.css({
						top: 0
					});
					minus = '';
				} else {
					$el.css({
						bottom: 0
					});
					minus = '-';
				}

				// Set Transition
				if( self.options.transition ) {
					$el.css({
						transition: 'ease-out transform '+ self.options.transitionDuration +'ms ' + self.options.transitionDelay + 'ms'
					});
				}

				// First Load
				self.movement(minus);	

				// Scroll
				$window.on('scroll', function(){
					self.movement(minus);				   
				});

			}

			return this;
		},

		movement: function(minus) {
			var self = this,
				$el = this.options.wrapper,
				$window = $(window),
				scrollTop = $window.scrollTop(),
		    	elementOffset = $el.offset().top,
		     	currentElementOffset = (elementOffset - scrollTop),
		     	factor = ( self.options.isInsideSVG ) ? 2 : 100;

		   	var scrollPercent = factor * currentElementOffset / ($window.height());

		   	if( $el.visible( true ) ) {

		   		if( !self.options.horizontal ) {

		   			$el.css({
			   			transform: 'translate3d(0, '+ minus + scrollPercent / self.options.speed +'%, 0)'
			   		});

		   		} else {

		   			$el.css({
			   			transform: 'translate3d('+ minus + scrollPercent / self.options.speed +'%, '+ minus + scrollPercent / self.options.speed +'%, 0)'
			   		});

		   		}
		   		
		   	}

		}
	};

	// expose to scope
	$.extend(theme, {
		PluginFloatElement: PluginFloatElement
	});

	// jquery plugin
	$.fn.themePluginFloatElement = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginFloatElement($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// GDPR
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__gdpr';

	var PluginGDPR = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginGDPR.defaults = {
		cookieBarShowDelay: 3000
	};

	PluginGDPR.prototype = {
		initialize: function($el, opts) {
			var self = this;

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build()
				.events();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginGDPR.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			var self = this;

			// Show
			if( !$.cookie( 'porto-privacy-bar' ) ) {
				setTimeout(function(){
					self.options.wrapper.addClass('show');
				}, self.options.cookieBarShowDelay);
			}

			// If already has preferences cookie, check inputs according preferences cookie data
			if( $.cookie( 'porto-gdpr-preferences' ) ) {
				var preferencesArr = $.cookie( 'porto-gdpr-preferences' ).split(',');

				for( var i = 0; i < preferencesArr.length; i++ ) {
					if( $('input[value="'+ preferencesArr[i] +'"]').get(0) ) {
						if( $('input[value="'+ preferencesArr[i] +'"]').is(':checkbox') ) {
							$('input[value="'+ preferencesArr[i] +'"]').prop('checked', true);
						}
					}
				}
			}

			return this;

		},

		events: function() {
			var self = this;

			// Agree Trigger
			self.options.wrapper.find('.gdpr-agree-trigger').on('click', function(e){
				e.preventDefault();

				$('.gdpr-preferences-form').find('.gdpr-input').each(function(){
					if( $(this).is(':checkbox') || $(this).is(':hidden') ) {
						$(this).prop('checked', true);
					}
				});

				$('.gdpr-preferences-form').trigger('submit').removeClass('show');

				self.removeCookieBar();
			});

			// Preferences Trigger
			self.options.wrapper.find('.gdpr-preferences-trigger').on('click', function(e){
				e.preventDefault();

				$('.gdpr-preferences-popup').addClass('show');
			});

			// Close Popup Button
			$('.gdpr-close-popup').on('click', function(e){
				e.preventDefault();

				$('.gdpr-preferences-popup').removeClass('show');
			});

			// Close Popup When Click Outside of popup area
			$('.gdpr-preferences-popup').on('click', function(e){
				if( !$(e.target).closest('.gdpr-preferences-popup-content').get(0) ) {
					$('.gdpr-preferences-popup').removeClass('show');
				}
			});

			// Preference Form
			$('.gdpr-preferences-form').on('submit', function(e){
				e.preventDefault();

				var $this = $(this);

				// Save Preferences Button
				$this.find('button[type="submit"]').text( 'SAVING...' );

				// Form Data
				var formData = [];
				$this.find('.gdpr-input').each(function(){
					if( $(this).is(':checkbox') && $(this).is(':checked') || $(this).is(':hidden') ) {
						formData.push( $(this).val() );
					}
				});

				$.cookie( 'porto-privacy-bar', true );

				setTimeout(function(){
					$this.find('button[type="submit"]').text( 'SAVED!' ).removeClass('btn-primary').addClass('btn-success');

					setTimeout(function(){
						$('.gdpr-preferences-popup').removeClass('show');
						self.removeCookieBar();

						$this.find('button[type="submit"]').text( 'SAVE PREFERENCES' ).removeClass('btn-success').addClass('btn-primary');

						if( $.cookie( 'porto-gdpr-preferences' ) ) {

							$.cookie( 'porto-gdpr-preferences', formData );
							location.reload();

						} else {

							$.cookie( 'porto-gdpr-preferences', formData );

							if ($.isFunction($.fn['themePluginGDPRWrapper']) && $('[data-plugin-gdpr-wrapper]').length) {

								$(function() {
									$('[data-plugin-gdpr-wrapper]:not(.manual)').each(function() {
										var $this = $(this),
											opts;

										$this.removeData('__gdprwrapper');

										var pluginOptions = theme.fn.getOptions($this.data('plugin-options'));
										if (pluginOptions)
											opts = pluginOptions;

										$this.themePluginGDPRWrapper(opts);
									});
								});

							}

						}

					}, 500);
				}, 1000);
			});

			// Remove/Reset Cookies
			$('.gdpr-reset-cookies').on('click', function(e){
				e.preventDefault();

				self.clearCookies();

				location.reload();
			});

			// Open Preferences
			$('.gdpr-open-preferences').on('click', function(e){
				e.preventDefault();

				$('.gdpr-preferences-popup').toggleClass('show');
			});

			return this;
		},

		removeCookieBar: function() {
			var self = this;

			self.options.wrapper.addClass('removing').on('transitionend', function(){
				setTimeout(function(){
					self.options.wrapper.removeClass('show removing');
				}, 500);
			});

			return this;
		},

		clearCookies: function() {
			var self = this;

			$.removeCookie( 'porto-privacy-bar' );
			$.removeCookie( 'porto-gdpr-preferences' );

			return this;
		}
	};

	// expose to scope
	$.extend(theme, {
		PluginGDPR: PluginGDPR
	});

	// jquery plugin
	$.fn.themePluginGDPR = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginGDPR($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// GDPR Wrapper
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__gdprwrapper';

	var PluginGDPRWrapper = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginGDPRWrapper.defaults = {

	};

	PluginGDPRWrapper.prototype = {
		initialize: function($el, opts) {
			var self = this;

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build();
				
			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginGDPRWrapper.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			var self = this;

			if( $.cookie( 'porto-gdpr-preferences' ) && $.cookie( 'porto-gdpr-preferences' ).indexOf( self.options.checkCookie ) != -1 ) {

				$.ajax({
					url: self.options.ajaxURL,
					cache: false,
					complete: function(data) {
					
						setTimeout(function() {

							self.options.wrapper.html(data.responseText).addClass('show');

						}, 1000);

					}
				});

			} else {
				self.options.wrapper.addClass('show');
			}

			return this;

		}
	};

	// expose to scope
	$.extend(theme, {
		PluginGDPRWrapper: PluginGDPRWrapper
	});

	// jquery plugin
	$.fn.themePluginGDPRWrapper = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginGDPRWrapper($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// Hover Effect
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__hoverEffect';

	var PluginHoverEffect = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginHoverEffect.defaults = {
		effect: 'magnetic',
		magneticMx: 0.15,
		magneticMy: 0.3,
		magneticDeg: 12,
		selector: '.thumb-info, .hover-effect-3d-wrapper',
		sensitivity: 20
	};

	PluginHoverEffect.prototype = {
		initialize: function($el, opts) {
			if ($el.data(instanceName)) {
				return this;
			}

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginHoverEffect.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			var self = this;

			if(self.$el.hasClass('hover-effect-3d')) {
				self.options.effect = '3d';
			}

			// Magnetic
			if(self.options.effect == 'magnetic') {
				self.magnetic();
			}

			// 3d
			if(self.options.effect == '3d') {
				self.hover3d();
			}

			return this;
		},

		magnetic: function() {
			var self = this;

			self.$el.mousemove(function(e) {

				const pos = this.getBoundingClientRect();
				const mx = e.clientX - pos.left - pos.width/2; 
				const my = e.clientY - pos.top - pos.height/2;

				this.style.transform = 'translate('+ mx * self.options.magneticMx +'px, '+ my * self.options.magneticMx +'px)';

			});

			self.$el.mouseleave(function(e) {

				this.style.transform = 'translate3d(0px, 0px, 0px)';

			});

			return this;

		},

		hover3d: function() {
			var self = this;

			if ($.isFunction($.fn['hover3d'])) {

				self.$el.hover3d({
					selector: self.options.selector,
					sensitivity: self.options.sensitivity
				});

			}

			return this;

		}
	};

	// expose to scope
	$.extend(theme, {
		PluginHoverEffect: PluginHoverEffect
	});

	// jquery plugin
	$.fn.themePluginHoverEffect = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginHoverEffect($this, opts);
			}

		});
	};

}).apply(this, [window.theme, jQuery]);

// Icon
(function(theme, $) {

	'use strict';

	theme = theme || {};

	var instanceName = '__icon';

	var PluginIcon = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginIcon.defaults = {
		color: '#2388ED',
		animated: false,
		delay: 300,
		onlySVG: false,
		removeClassAfterInit: false,
		fadeIn: true,
		accY: 0
	};

	PluginIcon.prototype = {
		initialize: function($el, opts) {
			if ($el.data(instanceName)) {
				return this;
			}

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginIcon.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			var self  	 = this,
				$el   	 = this.options.wrapper,
				color 	 = self.options.color,
				elTopDistance = $el.offset().top,
				windowTopDistance = $(window).scrollTop(),
				duration = ( self.options.animated && !self.options.strokeBased ) ? 200 : 100;

			// Check origin
			if( window.location.protocol === 'file:' ) {
				$el.css({
					opacity: 1,
					width: $el.attr('width')
				});

				if( self.options.extraClass ) {
					$el.addClass( self.options.extraClass );
				}

				if( self.options.extraClass.indexOf('-color-light') > 0 ) {
					$el.css({
						filter: 'invert(1)'
					});
				}

				$(window).trigger('icon.rendered');
				return;
			}

			// Duration
			if( self.options.duration ) {
				duration = self.options.duration;
			}

			// SVG Content
			var SVGContent = $.get({
				url: $el.attr('src'), 
				success: function(data, status, xhr){
					var iconWrapper = self.options.fadeIn ? $('<div class="animated-icon animated fadeIn">'+ xhr.responseText +'</div>') : $('<div class="animated-icon animated">'+ xhr.responseText +'</div>'),
						uniqid = 'icon_' + Math.floor(Math.random() * 26) + Date.now();

					// Add ID
					iconWrapper.find('svg').attr('id', uniqid);

					// Identify with filename
					iconWrapper.find('svg').attr('data-filename', $el.attr('src').split(/(\\|\/)/g).pop());

					if( $el.attr('width') ) {
						iconWrapper.find('svg')
							.attr('width', $el.attr('width'))
							.attr('height', $el.attr('width'));						
					}

					if( $el.attr('height') ) {
						iconWrapper.find('svg')
							.attr('height', $el.attr('height'));	
					}

					if( self.options.svgViewBox ) {
						iconWrapper.find('svg')
							.attr('viewBox', self.options.svgViewBox);
					}

					$el.replaceWith(iconWrapper);

					if( self.options.extraClass ) {
						iconWrapper.addClass( self.options.extraClass );
					}

					if( self.options.removeClassAfterInit ) {
						iconWrapper.removeClass(self.options.removeClassAfterInit);
					}

					if( self.options.onlySVG ) {
						$(window).trigger('icon.rendered');
						return this;
					}

					$el = iconWrapper;

					var icon = new Vivus(uniqid, {start: 'manual', type: 'sync', selfDestroy: true, duration: duration, onReady: function(obj){
						var styleElement = document.createElementNS("http://www.w3.org/2000/svg", "style"),
							animateStyle = '';

						// SVG Fill Based
						if( self.options.animated && !self.options.strokeBased || !self.options.animated && color && !self.options.strokeBased ) {
							animateStyle = 'stroke-width: 0.1px; fill-opacity: 0; transition: ease fill-opacity 300ms;';
							
							// Set Style on SVG inside object
							styleElement.textContent = '#' + uniqid + ' path, #' + uniqid + ' line, #' + uniqid + ' rect, #' + uniqid + ' circle, #' + uniqid + ' polyline { fill: '+ color +'; stroke: '+ color +'; '+ animateStyle + (self.options.svgStyle ? self.options.svgStyle : "") + ' } .finished path { fill-opacity: 1; }';
							obj.el.appendChild(styleElement);
						}

						// SVG Stroke Based
						if( self.options.animated && self.options.strokeBased || !self.options.animated && color && self.options.strokeBased ) {

							// Set Style on SVG inside object
							styleElement.textContent = '#' + uniqid + ' path, #' + uniqid + ' line, #' + uniqid + ' rect, #' + uniqid + ' circle, #' + uniqid + ' polyline { stroke: '+ color +'; ' + (self.options.svgStyle ? self.options.svgStyle : "") + '}';
							obj.el.appendChild(styleElement);
						}

						$.event.trigger('theme.plugin.icon.svg.ready');
					}});

					// Isn't animated
					if( !self.options.animated ) {
						setTimeout(function(){
							icon.finish();
						}, 10);
						$el.css({ opacity: 1 });
					}

					// Animated
					if( self.options.animated && $(window).width() > 767 ) {
						
						// First Load
						if( $el.visible( true ) ) {
							self.startIconAnimation( icon, $el );
						} else if( elTopDistance < windowTopDistance ) {
							self.startIconAnimation( icon, $el );
						}

						// On Scroll
						$(window).on('scroll', function(){
							if( $el.visible( true ) ) {
								self.startIconAnimation( icon, $el );
							}
						});

					} else {
						
						$el.css({ opacity: 1 });
						icon.finish();
						
						$(window).on('theme.plugin.icon.svg.ready', function(){
							setTimeout(function(){
								icon.el.setAttribute('class', 'finished');
								icon.finish();
							}, 300);
						});
						
					}

					$(window).trigger('icon.rendered');
				}
			});

			return this;
		},

		startIconAnimation: function(icon, $el) {
			var self = this;

			// Animate for better performance
			$({to:0}).animate({to:1}, ((self.options.strokeBased) ? self.options.delay : self.options.delay + 300 ), function() {
				$el.css({ opacity: 1 });
			});

			$({to:0}).animate({to:1}, self.options.delay, function() {
				icon.play(1);

				setTimeout(function(){
					icon.el.setAttribute('class', 'finished');
				}, icon.duration * 5 );
			});
		}
	};

	// expose to scope
	$.extend(theme, {
		PluginIcon: PluginIcon
	});

	// jquery plugin
	$.fn.themePluginIcon = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginIcon($this, opts);
			}

		});
	};

}).apply(this, [window.theme, jQuery]);

// Lightbox
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__lightbox';

	var PluginLightbox = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginLightbox.defaults = {
		tClose: 'Close (Esc)', // Alt text on close button
		tLoading: 'Loading...', // Text that is displayed during loading. Can contain %curr% and %total% keys
		gallery: {
			tPrev: 'Previous (Left arrow key)', // Alt text on left arrow
			tNext: 'Next (Right arrow key)', // Alt text on right arrow
			tCounter: '%curr% of %total%' // Markup for "1 of 7" counter
		},
		image: {
			tError: '<a href="%url%">The image</a> could not be loaded.' // Error message when image could not be loaded
		},
		ajax: {
			tError: '<a href="%url%">The content</a> could not be loaded.' // Error message when ajax request failed
		},
		callbacks: {
			open: function() {
				$('html').addClass('lightbox-opened');
			},
			close: function() {
				$('html').removeClass('lightbox-opened');
			}
		}
	};

	PluginLightbox.prototype = {
		initialize: function($el, opts) {
			if ($el.data(instanceName)) {
				return this;
			}

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginLightbox.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			if (!($.isFunction($.fn.magnificPopup))) {
				return this;
			}

			this.options.wrapper.magnificPopup(this.options);

			return this;
		}
	};

	// expose to scope
	$.extend(theme, {
		PluginLightbox: PluginLightbox
	});

	// jquery plugin
	$.fn.themePluginLightbox = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginLightbox($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// Loading Overlay
(function(theme, $) {

	'use strict';

	theme = theme || {};

	// Default
	var loadingOverlayDefaultTemplate = [
		'<div class="loading-overlay">',
			'<div class="bounce-loader"><div class="bounce1"></div><div class="bounce2"></div><div class="bounce3"></div></div>',
		'</div>'
	].join('');

	// Percentage
	var loadingOverlayPercentageTemplate = [
		'<div class="loading-overlay loading-overlay-percentage">',
			'<div class="page-loader-progress-wrapper"><span class="page-loader-progress">0</span><span class="page-loader-progress-symbol">%</span></div>',
		'</div>'
	].join('');

	// Cubes
	var loadingOverlayCubesTemplate = [
		'<div class="loading-overlay">',
			'<div class="bounce-loader"><div class="cssload-thecube"><div class="cssload-cube cssload-c1"></div><div class="cssload-cube cssload-c2"></div><div class="cssload-cube cssload-c4"></div><div class="cssload-cube cssload-c3"></div></div></div>',
		'</div>'
	].join('');

	// Cube Progress
	var loadingOverlayCubeProgressTemplate = [
		'<div class="loading-overlay">',
			'<div class="bounce-loader"><span class="cssload-cube-progress"><span class="cssload-cube-progress-inner"></span></span></div>',
		'</div>'
	].join('');

	// Float Rings
	var loadingOverlayFloatRingsTemplate = [
		'<div class="loading-overlay">',
			'<div class="bounce-loader"><div class="cssload-float-rings-loader"><div class="cssload-float-rings-inner cssload-one"></div><div class="cssload-float-rings-inner cssload-two"></div><div class="cssload-float-rings-inner cssload-three"></div></div></div>',
		'</div>'
	].join('');

	// Floating Bars
	var loadingOverlayFloatBarsTemplate = [
		'<div class="loading-overlay">',
			'<div class="bounce-loader"><div class="cssload-float-bars-container"><ul class="cssload-float-bars-flex-container"><li><span class="cssload-float-bars-loading"></span></li></div></div></div>',
		'</div>'
	].join('');

	// Speeding Wheel
	var loadingOverlaySpeedingWheelTemplate = [
		'<div class="loading-overlay">',
			'<div class="bounce-loader"><div class="cssload-speeding-wheel-container"><div class="cssload-speeding-wheel"></div></div></div>',
		'</div>'
	].join('');

	// Zenith
	var loadingOverlayZenithTemplate = [
		'<div class="loading-overlay">',
			'<div class="bounce-loader"><div class="cssload-zenith-container"><div class="cssload-zenith"></div></div></div>',
		'</div>'
	].join('');

	// Spinning Square
	var loadingOverlaySpinningSquareTemplate = [
		'<div class="loading-overlay">',
			'<div class="bounce-loader"><div class="cssload-spinning-square-loading"></div></div>',
		'</div>'
	].join('');

	// Pulse
	var loadingOverlayPulseTemplate = [
		'<div class="loading-overlay">',
			'<div class="bounce-loader"><div class="wrapper-pulse"><div class="cssload-pulse-loader"></div></div></div>',
		'</div>'
	].join('');

	var LoadingOverlay = function( $wrapper, options, noInheritOptions ) {
		return this.initialize( $wrapper, options, noInheritOptions );
	};

	LoadingOverlay.prototype = {

		options: {
			css: {},
			hideDelay: 500,
			progressMinTimeout: 0,
			effect: 'default'
		},

		initialize: function( $wrapper, options, noInheritOptions ) {
			this.$wrapper = $wrapper;

			this
				.setVars()
				.setOptions( options, noInheritOptions )
				.build()
				.events()
				.dynamicShowHideEvents();

			this.$wrapper.data( 'loadingOverlay', this );
		},

		setVars: function() {
			this.$overlay = this.$wrapper.find('.loading-overlay');
			this.pageStatus = null;
			this.progress = null;
			this.animationInterval = 33;

			return this;
		},

		setOptions: function( options, noInheritOptions ) {
			if ( !this.$overlay.get(0) ) {
				this.matchProperties();
			}
			
			if( noInheritOptions ) {
				this.options     = $.extend( true, {}, this.options, options );
			} else {
				this.options     = $.extend( true, {}, this.options, options, theme.fn.getOptions(this.$wrapper.data('plugin-options')) );
			}

			this.loaderClass = this.getLoaderClass( this.options.css.backgroundColor );

			return this;
		},

		build: function() {
			var _self = this;

			if ( !this.$overlay.closest(document.documentElement).get(0) ) {
				if ( !this.$cachedOverlay ) {

					switch ( _self.options.effect ) {
						case 'percentageProgress1':
							this.$overlay = $( loadingOverlayPercentageTemplate ).clone();
							break;

						case 'percentageProgress2':
							this.$overlay = $( loadingOverlayPercentageTemplate ).clone();
							this.$overlay
								.addClass('loading-overlay-percentage-effect-2')
								.prepend('<div class="loading-overlay-background-layer"></div>');
							break;

						case 'cubes':
							this.$overlay = $( loadingOverlayCubesTemplate ).clone();
							break;

						case 'cubeProgress':
							this.$overlay = $( loadingOverlayCubeProgressTemplate ).clone();
							break;

						case 'floatRings':
							this.$overlay = $( loadingOverlayFloatRingsTemplate ).clone();
							break;

						case 'floatBars':
							this.$overlay = $( loadingOverlayFloatBarsTemplate ).clone();
							break;

						case 'speedingWheel':
							this.$overlay = $( loadingOverlaySpeedingWheelTemplate ).clone();
							break;

						case 'zenith':
							this.$overlay = $( loadingOverlayZenithTemplate ).clone();
							break;

						case 'spinningSquare':
							this.$overlay = $( loadingOverlaySpinningSquareTemplate ).clone();
							break;

						case 'pulse':
							this.$overlay = $( loadingOverlayPulseTemplate ).clone();
							break;

						case 'default':
						default:
							this.$overlay = $( loadingOverlayDefaultTemplate ).clone();
							break;
					}
					
					if ( this.options.css ) {
						this.$overlay.css( this.options.css );
						this.$overlay.find( '.loader' ).addClass( this.loaderClass );
					}
				} else {
					this.$overlay = this.$cachedOverlay.clone();
				}

				this.$wrapper.prepend( this.$overlay );
			}

			if ( !this.$cachedOverlay ) {
				this.$cachedOverlay = this.$overlay.clone();
			}

			if( ['percentageProgress1', 'percentageProgress2'].includes(_self.options.effect) ) {
				_self.updateProgress();

				if( _self.options.isDynamicHideShow ) {
					setTimeout(function(){
						_self.progress = 'complete';
						
						$('.page-loader-progress').text(100);

						if( ['percentageProgress2'].includes(_self.options.effect) ) {
			            	$('.loading-overlay-background-layer').css({
			            		width: '100%'
			            	});
			            }
					}, 2800);
				}
			}

			return this;
		},

		events: function() {
			var _self = this;

			if ( this.options.startShowing ) {
				_self.show();
			}

			if ( this.$wrapper.is('body') || this.options.hideOnWindowLoad ) {
				$( window ).on( 'load error', function() {
					setTimeout(function(){
						_self.hide();
					}, _self.options.progressMinTimeout);
				});
			}

			if ( this.options.listenOn ) {
				$( this.options.listenOn )
					.on( 'loading-overlay:show beforeSend.ic', function( e ) {
						e.stopPropagation();
						_self.show();
					})
					.on( 'loading-overlay:hide complete.ic', function( e ) {
						e.stopPropagation();
						_self.hide();
					});
			}

			this.$wrapper
				.on( 'loading-overlay:show beforeSend.ic', function( e ) {
					if ( e.target === _self.$wrapper.get(0) ) {
						e.stopPropagation();
						_self.show();
						return true;
					}
					return false;
				})
				.on( 'loading-overlay:hide complete.ic', function( e ) {
					if ( e.target === _self.$wrapper.get(0) ) {
						e.stopPropagation();
						_self.hide();
						return true;
					}
					return false;
				});

			if( ['percentageProgress1', 'percentageProgress2'].includes(_self.options.effect) ) {
				$(window).on('load', function(){
		            setTimeout(function(){
			            _self.pageStatus = "complete";

			            $('.page-loader-progress').text(100);

			            if( ['percentageProgress2'].includes(_self.options.effect) ) {
			            	$('.loading-overlay-background-layer').css({
			            		width: '100%'
			            	});
			            }
		            }, _self.options.progressMinTimeout);
				});
			}
		        
			return this;
		},

		show: function() {
			this.build();

			this.position = this.$wrapper.css( 'position' ).toLowerCase();
			if ( this.position != 'relative' || this.position != 'absolute' || this.position != 'fixed' ) {
				this.$wrapper.css({
					position: 'relative'
				});
			}
			this.$wrapper.addClass( 'loading-overlay-showing' );
		},

		hide: function() {
			var _self = this;

			setTimeout(function() {
				_self.$wrapper.removeClass( 'loading-overlay-showing' );
				
				if ( this.position != 'relative' || this.position != 'absolute' || this.position != 'fixed' ) {
					_self.$wrapper.css({ position: '' });
				}

				$(window).trigger('loading.overlay.ready');
			}, _self.options.hideDelay);
		},

		updateProgress: function() {
			var _self = this;

			var render = function() {
				if(_self.pageStatus == "complete"){
		            $('.page-loader-progress').text(100);
		            setTimeout(function(){
		                $('.page-loader-progress').addClass('d-none');    
		            }, 700);
		        }
		        else{            
		            if(_self.progress == null){
		                _self.progress = 1;
		            }
		           
		            _self.progress = _self.progress + 1;
		            if(_self.progress >= 0 && _self.progress <= 30){
		                _self.animationInterval += 1;
		                $('.page-loader-progress').text(_self.progress);
		            }
		            else if(_self.progress > 30 && _self.progress <= 60){
		                _self.animationInterval += 2;
		                $('.page-loader-progress').text(_self.progress);
		            }
		            else if(_self.progress > 60 && _self.progress <= 80){
		                _self.animationInterval += 40;
		                $('.page-loader-progress').text(_self.progress);
		            }
		            else if(_self.progress > 80 && _self.progress <= 90){
		                _self.animationInterval += 80;
		                $('.page-loader-progress').text(_self.progress);
		            }
		            else if(_self.progress > 90 && _self.progress <= 95){
		                _self.animationInterval += 150;
		                $('.page-loader-progress').text(_self.progress);
		            }
		            else if(_self.progress > 95 && _self.progress <= 99){
		                _self.animationInterval += 400;
		                $('.page-loader-progress').text(_self.progress);
		            }
		            else if(_self.progress >= 100){
		                $('.page-loader-progress').text(99);
		            }

		            if( ['percentageProgress2'].includes(_self.options.effect) ) {
		            	$('.loading-overlay-background-layer').css({
		            		width: _self.progress + '%'
		            	});
		            }
		              
					self.loopInside = setTimeout(render, _self.animationInterval);
		        }

			}
			render();

			return this;
		},

		matchProperties: function() {
			var i,
				l,
				properties;

			properties = [
				'backgroundColor',
				'borderRadius'
			];

			l = properties.length;

			for( i = 0; i < l; i++ ) {
				var obj = {};
				obj[ properties[ i ] ] = this.$wrapper.css( properties[ i ] );

				$.extend( this.options.css, obj );
			}
		},

		getLoaderClass: function( backgroundColor ) {
			if ( !backgroundColor || backgroundColor === 'transparent' || backgroundColor === 'inherit' ) {
				return 'black';
			}

			var hexColor,
				r,
				g,
				b,
				yiq;

			var colorToHex = function( color ){
				var hex,
					rgb;

				if( color.indexOf('#') >- 1 ){
					hex = color.replace('#', '');
				} else {
					rgb = color.match(/\d+/g);
					hex = ('0' + parseInt(rgb[0], 10).toString(16)).slice(-2) + ('0' + parseInt(rgb[1], 10).toString(16)).slice(-2) + ('0' + parseInt(rgb[2], 10).toString(16)).slice(-2);
				}

				if ( hex.length === 3 ) {
					hex = hex + hex;
				}

				return hex;
			};

			hexColor = colorToHex( backgroundColor );

			r = parseInt( hexColor.substr( 0, 2), 16 );
			g = parseInt( hexColor.substr( 2, 2), 16 );
			b = parseInt( hexColor.substr( 4, 2), 16 );
			yiq = ((r * 299) + (g * 587) + (b * 114)) / 1000;

			return ( yiq >= 128 ) ? 'black' : 'white';
		},

		dynamicShowHide: function( effect ) {
			var _self = this;

			// Remove Loading Overlay Data
			$('body').removeData('loadingOverlay');

			// Remove Html Of Loading Overlay
			$('.loading-overlay').remove();

			if( effect == '' ) {
				return this;
			}

			// Initialize New Loading Overlay (second parameter is to NO inherit data-plugin-options)
			$('body').loadingOverlay({
				effect: effect ? effect : 'pulse',
				isDynamicHideShow: true
			}, true);

			// Show Loading Overlay Loader
			$('body').data('loadingOverlay').show();

			// Hide Loading Overlay Loader
			setTimeout(function(){
				$('body').data('loadingOverlay').hide();
			}, 3000);

			return this;
		},

		dynamicShowHideEvents: function() {
			var _self = this;

			// Button
			$(document).off('click.loading-overlay-button').on('click.loading-overlay-button', '.loading-overlay-button', function(e){
				e.preventDefault();

				_self.dynamicShowHide( $(this).data('effect') );
			});

			// Select
			$(document).off('change.loading-overlay-select').on('change.loading-overlay-select', '.loading-overlay-select', function(){
				_self.dynamicShowHide( $(this).val() );
			});

			return this;
		}

	};

	// expose to scope
	$.extend(theme, {
		LoadingOverlay: LoadingOverlay
	});

	// expose as a jquery plugin
	$.fn.loadingOverlay = function( opts, noInheritOptions ) {
		return this.each(function() {
			var $this = $( this );

			var loadingOverlay = $this.data( 'loadingOverlay' );
			if ( loadingOverlay ) {
				return loadingOverlay;
			} else {
				var options = opts || $this.data( 'loading-overlay-options' ) || {};
				return new LoadingOverlay( $this, options, noInheritOptions );
			}
		});
	}

	// auto init
	$('[data-loading-overlay]').loadingOverlay();

}).apply(this, [window.theme, jQuery]);

// Masonry
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__masonry';

	var PluginMasonry = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginMasonry.defaults = {

	};

	PluginMasonry.prototype = {
		initialize: function($el, opts) {
			if ($el.data(instanceName)) {
				return this;
			}

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginMasonry.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			if (!($.isFunction($.fn.isotope))) {
				return this;
			}

			var self = this,
				$window = $(window);

			self.$loader = false;

			if (self.options.wrapper.parents('.masonry-loader').get(0)) {
				self.$loader = self.options.wrapper.parents('.masonry-loader');
				self.createLoader();
			}

			self.options.wrapper.one('layoutComplete', function(event, laidOutItems) {
				self.removeLoader();
			});

			self.options.wrapper.waitForImages(function() {
				self.options.wrapper.isotope(self.options);	
			});

			// IE10/11 fix
			if( $('html').hasClass('ie10') || $('html').hasClass('ie11') ) {
				var padding = parseInt( self.options.wrapper.children().css('padding-left') ) + parseInt( self.options.wrapper.children().css('padding-right') );
			}

			$(window).on('resize', function() {
				setTimeout(function() {
					self.options.wrapper.isotope('layout');
				}, 300);
			});

			setTimeout(function() {
				self.removeLoader();
			}, 3000);

			return this;
		},

		createLoader: function() {
			var self = this;

			var loaderTemplate = [
				'<div class="bounce-loader">',
					'<div class="bounce1"></div>',
					'<div class="bounce2"></div>',
					'<div class="bounce3"></div>',
				'</div>'
			].join('');

			self.$loader.append(loaderTemplate);

			return this;
		},

		removeLoader: function() {

			var self = this;

			if (self.$loader) {

				self.$loader.removeClass('masonry-loader-showing');

				setTimeout(function() {
					self.$loader.addClass('masonry-loader-loaded');
				}, 300);

			}

		}
	};

	// expose to scope
	$.extend(theme, {
		PluginMasonry: PluginMasonry
	});

	// jquery plugin
	$.fn.themePluginMasonry = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginMasonry($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// Match Height
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__matchHeight';

	var PluginMatchHeight = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginMatchHeight.defaults = {
		byRow: true,
		property: 'height',
		target: null,
		remove: false
	};

	PluginMatchHeight.prototype = {
		initialize: function($el, opts) {
			if ($el.data(instanceName)) {
				return this;
			}

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginMatchHeight.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			if (!($.isFunction($.fn.matchHeight))) {
				return this;
			}

			var self = this;

			self.options.wrapper.matchHeight(self.options);

			return this;
		}

	};

	// expose to scope
	$.extend(theme, {
		PluginMatchHeight: PluginMatchHeight
	});

	// jquery plugin
	$.fn.themePluginMatchHeight = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginMatchHeight($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// Parallax
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__parallax';

	var PluginParallax = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginParallax.defaults = {
		speed: 1.5,
		horizontalPosition: '50%',
		offset: 0,
		parallaxDirection: 'top',
		parallaxHeight: '180%',
		scrollableParallax: false,
		scrollableParallaxMinWidth: 991,
		startOffset: 7,
		transitionDuration: '200ms',
		cssProperty: 'width',
		cssValueStart: 40,
		cssValueEnd: 100,
		cssValueUnit: 'vw',
		mouseParallax: false
	};

	PluginParallax.prototype = {
		initialize: function($el, opts) {
			if ($el.data(instanceName)) {
				return this;
			}

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginParallax.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			var self = this,
				$window = $(window),
				offset,
				yPos,
				bgpos,
				background,
				rotateY;

			// Mouse Parallax
			if( self.options.mouseParallax ) {

				$window.mousemove(function(e) {

					$('.parallax-mouse-object', self.options.wrapper).each(function() {

				        var moving_value = $( this ).attr('data-value');
				        var x = (e.clientX * moving_value) / 250;
				        var y = (e.clientY * moving_value) / 250;

				        $( this ).css('transform', 'translateX(' + x + 'px) translateY(' + y + 'px)');
				    });

				});

				return this;

			}

			// Scrollable
			if( self.options.scrollableParallax && $(window).width() > self.options.scrollableParallaxMinWidth ) {
				var $scrollableWrapper = self.options.wrapper.find('.scrollable-parallax-wrapper');

				if( $scrollableWrapper.get(0) ) {

					var progress 	 = ( $(window).scrollTop() > ( self.options.wrapper.offset().top + $(window).outerHeight() ) ) ? self.options.cssValueEnd : self.options.cssValueStart,
						cssValueUnit = self.options.cssValueUnit ? self.options.cssValueUnit : '';

					$scrollableWrapper.css({
						'background-image' : 'url(' + self.options.wrapper.data('image-src') + ')',
						'background-size' : 'cover',
						'background-position' : 'center',
						'background-attachment' : 'fixed',
						'transition' : 'ease '+ self.options.cssProperty +' '+ self.options.transitionDuration,
						'width' : progress + '%'
					});

					$(window).on('scroll', function(e){
						if( self.options.wrapper.visible( true ) ) {
							var $window = $(window),
								scrollTop = $window.scrollTop(),
						    	elementOffset = self.options.wrapper.offset().top,
						     	currentElementOffset = (elementOffset - scrollTop);

						   	var scrollPercent = Math.abs( +( currentElementOffset - $window.height() ) / (self.options.startOffset ? self.options.startOffset : 7) );
						 	
						 	// Increment progress value according scroll position
						 	if( scrollPercent <= self.options.cssValueEnd && progress <= self.options.cssValueEnd ) {
						 		progress = self.options.cssValueStart + scrollPercent;
						 	}

						 	// Adjust CSS end value
						 	if( progress > self.options.cssValueEnd ) {
						 		progress = self.options.cssValueEnd;
						 	}

						 	// Adjust CSS start value
						 	if( progress < self.options.cssValueStart ) {
						 		progress = self.options.cssValueStart;
						 	}

						 	var styles = {}
						 	styles[self.options.cssProperty] = progress + cssValueUnit;

							$scrollableWrapper.css(styles);
						}
					});

				}

				return;
			}

			// Create Parallax Element
			if( self.options.fadeIn ) {
				background = $('<div class="parallax-background fadeIn animated"></div>');
			} else {
				background = $('<div class="parallax-background"></div>');
			}

			// Set Style for Parallax Element
			background.css({
				'background-image' : 'url(' + self.options.wrapper.data('image-src') + ')',
				'background-size' : 'cover',
				'position' : 'absolute',
				'top' : 0,
				'left' : 0,
				'width' : '100%',
				'height' : self.options.parallaxHeight
			});

			// Add Parallax Element on DOM
			self.options.wrapper.prepend(background);

			// Set Overlfow Hidden and Position Relative to Parallax Wrapper
			self.options.wrapper.css({
				'position' : 'relative',
				'overflow' : 'hidden'
			});

			// Parallax Effect on Scroll & Resize
			var parallaxEffectOnScrolResize = function() {
				$window.on('scroll resize', function() {
					offset  = self.options.wrapper.offset();
					yPos    = -($window.scrollTop() - (offset.top - 100)) / ((self.options.speed + 2 ));
					plxPos  = (yPos < 0) ? Math.abs(yPos) : -Math.abs(yPos);
					rotateY = ( $('html[dir="rtl"]').get(0) ) ? ' rotateY(180deg)' : ''; // RTL

					if( self.options.parallaxDirection == 'bottom' ) {
						self.options.offset = 250;
					}

					var y = ( (plxPos - 50) + (self.options.offset) );
					if( self.options.parallaxDirection == 'bottom' ) {
						y = ( y < 0 ) ? Math.abs( y ) : -Math.abs( y );
					}

					background.css({
						'transform' : 'translate3d(0, '+ y +'px, 0)' + rotateY,
						'background-position-x' : self.options.horizontalPosition
					});
				});

				$window.trigger('scroll');
			}

			if (!$.browser.mobile) {
				parallaxEffectOnScrolResize();
			} else {
				if( self.options.enableOnMobile == true ) {
					parallaxEffectOnScrolResize();
				} else {
					self.options.wrapper.addClass('parallax-disabled');
				}
			}

			return this;
		}
	};

	// expose to scope
	$.extend(theme, {
		PluginParallax: PluginParallax
	});

	// jquery plugin
	$.fn.themePluginParallax = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginParallax($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// Progress Bar
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__progressBar';

	var PluginProgressBar = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginProgressBar.defaults = {
		accX: 0,
		accY: -50,
		delay: 1
	};

	PluginProgressBar.prototype = {
		initialize: function($el, opts) {
			if ($el.data(instanceName)) {
				return this;
			}

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginProgressBar.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			var self = this,
				$el = this.options.wrapper,
				delay = 1;

			delay = ($el.attr('data-appear-animation-delay') ? $el.attr('data-appear-animation-delay') : self.options.delay);

			$el.addClass($el.attr('data-appear-animation'));

			setTimeout(function() {

				$el.animate({
					width: $el.attr('data-appear-progress-animation')
				}, 1500, 'easeOutQuad', function() {
					$el.find('.progress-bar-tooltip').animate({
						opacity: 1
					}, 500, 'easeOutQuad');
				});

			}, delay);

			return this;
		}
	};

	// expose to scope
	$.extend(theme, {
		PluginProgressBar: PluginProgressBar
	});

	// jquery plugin
	$.fn.themePluginProgressBar = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginProgressBar($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// Random Images
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__randomimages';

	var PluginRandomImages = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginRandomImages.defaults = {
		minWindowWidth: 0,
		random: true,
		imagesListURL: null,
		lightboxImagesListURL: null,
        delay: null,
        animateIn: 'fadeIn',
		animateOut: 'fadeOut',
		stopAtImageIndex: false, // The value shoudl be the index value of array with images as string. Eg: '2' 
		stopAfterFewSeconds: false, // The value should be in mili-seconds. Eg: 10000 = 10 seconds
		stopAfterXTimes: false,
		accY: 0
	};

	PluginRandomImages.prototype = {
		initialize: function($el, opts) {
			this.$el = $el;
            this.st = '';
			this.times = 0;
			this.perImageIndex = 0;

            if( $el.is('img') && typeof opts.imagesListURL == 'undefined' ) {
                return false;
            }

            this
				.setData()
				.setOptions(opts)
				.build();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginRandomImages.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
            var self = this;
			
			// Control the screens size we want to have the plugin working
			if( $(window).width() < self.options.minWindowWidth  ) {
				return false;
			}

			// Check if is single image or wrapper with images inside
            if( self.$el.is('img') ) {
				
				// Check it's inside a lightbox
				self.isInsideLightbox = self.$el.closest('.lightbox').length ? true : false;

				// Push the initial image to lightbox list/array
				if( self.isInsideLightbox && self.options.lightboxImagesListURL ) {
					self.options.lightboxImagesListURL.push( self.$el.closest('.lightbox').attr('href') );
				}
	
				// Push the current image src to the array
				self.options.imagesListURL.push( self.$el.attr('src') );

				// Start with lastIndex as the first image loaded on the page
				self.lastIndex = self.options.imagesListURL.length - 1;

				// Identify the last random image element (if has more than one on the page)
				if( self.options.random == false ) {
					$('.plugin-random-images').each(function(i){
						if( i == $('.plugin-random-images').length - 1 ) {
							$(this).addClass('the-last');
						}
					});
				}

				// Start the recursive timeout
				setTimeout(function(){
					self.recursiveTimeout( 
						self.perImageTag, 
						self.options.delay == null ? 3000 : self.options.delay
					);
				}, self.options.delay == null ? 300 : self.options.delay / 3);

			} else {
				
				// Start the recursive timeout
				setTimeout( self.recursiveTimeout( 
					self.perWrapper, 
					self.options.delay ? self.options.delay : getPerWrapperHighDelay(), 
					false 
				), 300);

			}

			// Stop After Few Seconds
			if( self.options.stopAfterFewSeconds ) {
				setTimeout(function(){
					clearTimeout(self.st);
				}, self.options.stopAfterFewSeconds);
			}
			
			return this;

		},

		perImageTag: function() {
			var self = this;

			// Generate a random index to make the images rotate randomly
			var index = self.options.random ? Math.floor(Math.random() * self.options.imagesListURL.length) : self.lastIndex;

			// Avoid repeat the same image
			if( self.lastIndex !== '' && self.lastIndex == index ) {
				if( self.options.random ) {
					while( index == self.lastIndex ) {
						index = Math.floor(Math.random() * self.options.imagesListURL.length);
					}
				} else {
					index = index - 1;
					if( index == -1 ) {
						index = self.options.imagesListURL.length - 1;
					}
				}
			}

			// Turn the image ready for animations
			self.$el.addClass('animated');

			// Remove the entrance animation class and add the out animation class
			self.$el.removeClass( self.options.animateIn ).addClass( self.options.animateOut );
			
			// Change the image src and add the class for entrance animation
			setTimeout( function(){
				self.$el.attr('src', self.options.imagesListURL[index]).removeClass( self.options.animateOut ).addClass(self.options.animateIn);

				if( self.isInsideLightbox && self.options.lightboxImagesListURL ) {
					self.$el.closest('.lightbox').attr('href', self.options.lightboxImagesListURL[index]);
				}
			}, 1000);
			
			// Save the last index for future checks
			self.lastIndex = index;
			
			// Increment the times var
			self.times++;

			// Save the index for stopAtImageIndex option
			self.perImageIndex = index;

			return this;
		},

		// Iterate the imaes loop and get the higher value
		getPerWrapperHighDelay: function() {
			var self = this,
				$wrapper = self.$el,
				delay = 0;

			$wrapper.find('img').each(function(){
				var $image = $(this);
				
				if( $image.data('rimage-delay') && parseInt( $image.data('rimage-delay') ) > delay ) {
					delay = parseInt( $image.data('rimage-delay') );
				}
			});

			return delay;
		},

		perWrapper: function() {
			var self = this,
				$wrapper = self.$el;

			// Turns the imageLlistURL into an array
			self.options.imagesListURL = [];

			// Find all images inside the element wrapper and push their sources to image list array
			$wrapper.find('img').each(function(){
				var $image = $(this);
				self.options.imagesListURL.push( $image.attr('src') ); 
			});

			// Shuffle the images list array (random effect)
			self.options.imagesListURL = self.shuffle( self.options.imagesListURL );

			// Iterate over each image and make some checks like delay for each image, animations, etc...
			$wrapper.find('img').each(function(index){
				var $image = $(this),
					animateIn  = $image.data('rimage-animate-in') ? $image.data('rimage-animate-in') : self.options.animateIn,
					animateOut = $image.data('rimage-animate-out') ? $image.data('rimage-animate-out') : self.options.animateOut,
					delay      = $image.data('rimage-delay') ? $image.data('rimage-delay') : 2000;

				$image.addClass('animated');

				setTimeout( function(){
					$image.removeClass( animateIn ).addClass( animateOut );
				}, delay / 2);

				setTimeout( function(){
					$image.attr('src', self.options.imagesListURL[index]).removeClass( animateOut ).addClass(animateIn);
				}, delay);

			});
			
			// Increment the times variable
			self.times++;

			return this;
		},

		recursiveTimeout: function(callback, delay) {
			var self = this;

			var timeout = function() {

				if( callback !== null ) {
					callback.call(self);
				}

				// Recursive
				self.st = setTimeout(timeout, delay == null ? 1000 : delay);

				if( self.options.random == false ) {
					if( self.$el.hasClass('the-last') ) {
						$('.plugin-random-images').trigger('rimages.start');
					} else {
						clearTimeout(self.st);
					}
				}

				// Stop At Image Index
				if( self.options.stopAtImageIndex && parseInt(self.options.stopAtImageIndex) == self.perImageIndex ) {
					clearTimeout(self.st);
				}

				// Stop After X Timers
				if( self.options.stopAfterXTimes == self.times ) {
					clearTimeout(self.st);
				}
			}
			timeout();

			self.$el.on('rimages.start', function(){
				clearTimeout(self.st);
				self.st = setTimeout(timeout, delay == null ? 1000 : delay);
			});

		},
		
		shuffle: function( array ) {
			for (var i = array.length - 1; i > 0; i--) {
				var j = Math.floor(Math.random() * (i + 1));
				var temp = array[i];
				array[i] = array[j];
				array[j] = temp;
			}

			return array;
		}

	};

	// expose to scope
	$.extend(theme, {
		PluginRandomImages: PluginRandomImages
	});

	// jquery plugin
	$.fn.themePluginRandomImages = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginRandomImages($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// Read More
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__readmore';

	var PluginReadMore = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginReadMore.defaults = {
		buttonOpenLabel: 'Read More <i class="fas fa-chevron-down text-2 ms-1"></i>',
		buttonCloseLabel: 'Read Less <i class="fas fa-chevron-up text-2 ms-1"></i>',
		enableToggle: true,
		maxHeight: 110,
		overlayColor: '#FFF',
		overlayHeight: 100,
		startOpened: false,
		align: 'left'
	};

	PluginReadMore.prototype = {
		initialize: function($el, opts) {
			var self = this;

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build()
				.events();

			if( self.options.startOpened ) {
				self.options.wrapper.find('.readmore-button-wrapper > a').trigger('click');
			}

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginReadMore.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			var self = this;

			self.options.wrapper.addClass('position-relative');

			// Overlay
			self.options.wrapper.append( '<div class="readmore-overlay"></div>' );

			// Check if is Safari
			var backgroundCssValue = 'linear-gradient(180deg, rgba(2, 0, 36, 0) 0%, '+ self.options.overlayColor +' 100%)';
			if( $('html').hasClass('safari') ) {
				backgroundCssValue = '-webkit-linear-gradient(top, rgba(2, 0, 36, 0) 0%, '+ self.options.overlayColor +' 100%)'
			}
			
			self.options.wrapper.find('.readmore-overlay').css({
				background: backgroundCssValue,
				position: 'absolute',
				bottom: 0,
				left: 0,
				width: '100%',
				height: self.options.overlayHeight,
				'z-index': 1
			});

			// Read More Button
			self.options.wrapper.find('.readmore-button-wrapper').removeClass('d-none').css({
				position: 'absolute',
				bottom: 0,
				left: 0,
				width: '100%',
				'z-index': 2
			});	

			// Button Label
			self.options.wrapper.find('.readmore-button-wrapper > a').html( self.options.buttonOpenLabel );

			self.options.wrapper.css({
				'height': self.options.maxHeight,
				'overflow-y': 'hidden'
			});

			// Alignment
			switch ( self.options.align ) {
				case 'center':
					self.options.wrapper.find('.readmore-button-wrapper').addClass('text-center');
					break;

				case 'right':
					self.options.wrapper.find('.readmore-button-wrapper').addClass('text-right');
					break;

				case 'left':
				default:
					self.options.wrapper.find('.readmore-button-wrapper').addClass('text-left');
					break;
			}

			return this;

		},

		events: function() {
			var self = this;

			// Read More
			self.readMore = function() {
				self.options.wrapper.find('.readmore-button-wrapper > a:not(.readless)').on('click', function(e){
					e.preventDefault();

					var $this = $(this);

					setTimeout(function(){
						self.options.wrapper.animate({
							'height': self.options.wrapper[0].scrollHeight
						}, function(){
							if( !self.options.enableToggle ) {
								$this.fadeOut();
							}

							$this.html( self.options.buttonCloseLabel ).addClass('readless').off('click');

							self.readLess();

							self.options.wrapper.find('.readmore-overlay').fadeOut();
							self.options.wrapper.css({
								'max-height': 'none',
								'overflow': 'visible'
							});

							self.options.wrapper.find('.readmore-button-wrapper').animate({
								bottom: -20
							});
						});
					}, 200);
				});
			}

			// Read Less
			self.readLess = function() {
				self.options.wrapper.find('.readmore-button-wrapper > a.readless').on('click', function(e){
					e.preventDefault();

					var $this = $(this);

					// Button
					self.options.wrapper.find('.readmore-button-wrapper').animate({
						bottom: 0
					});

					// Overlay
					self.options.wrapper.find('.readmore-overlay').fadeIn();

					setTimeout(function(){
						self.options.wrapper.height(self.options.wrapper[0].scrollHeight).animate({
							'height': self.options.maxHeight
						}, function(){
							$this.html( self.options.buttonOpenLabel ).removeClass('readless').off('click');

							self.readMore();

							self.options.wrapper.css({
								'overflow': 'hidden'
							});
						});
					}, 200);
				});
			}

			// First Load
			self.readMore();

			return this;
		}
	};

	// expose to scope
	$.extend(theme, {
		PluginReadMore: PluginReadMore
	});

	// jquery plugin
	$.fn.themePluginReadMore = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginReadMore($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// Revolution Slider
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__revolution';

	var PluginRevolutionSlider = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginRevolutionSlider.defaults = {
		sliderType: 'standard',
		sliderLayout: 'fullwidth',
		delay: 9000,
		gridwidth: 1170,
		gridheight: 500,
		spinner: 'spinner3',
		disableProgressBar: 'on',
		parallax: {
			type: 'off',
			bgparallax: 'off'
		},
		navigation: {
			keyboardNavigation: 'off',
			keyboard_direction: 'horizontal',
			mouseScrollNavigation: 'off',
			onHoverStop: 'off',
			touch: {
				touchenabled: 'on',
				swipe_threshold: 75,
				swipe_min_touches: 1,
				swipe_direction: 'horizontal',
				drag_block_vertical: false
			},
			arrows: {
				enable: true,
				hide_onmobile: false,
				hide_under: 0,
				hide_onleave: true,
				hide_delay: 200,
				hide_delay_mobile: 1200,
				left: {
					h_align: 'left',
					v_align: 'center',
					h_offset: 30,
					v_offset: 0
				},
				right: {
					h_align: 'right',
					v_align: 'center',
					h_offset: 30,
					v_offset: 0
				}
			}
		},

		/* ADDONS */
	    addOnTypewriter: {
			enable: false
		},
		addOnWhiteboard: {
			enable: false,

		},
	    whiteboard: {
	        movehand: {
	            src: '../vendor/rs-plugin/revolution-addons/whiteboard/assets/images/hand_point_right.png',
	            width: 400,
	            height: 1000,
	            handtype: 'right',
	            transform: {
	                transformX: 50,
	                transformY: 50
	            },
	            jittering: {
	                distance: '80',
	                distance_horizontal: '100',
	                repeat: '5',
	                offset: '10',
	                offset_horizontal: '0'
	            },
	            rotation: {
	                angle: '10',
	                repeat: '3'
	            }
	        },
	        writehand: {
	            src: '../vendor/rs-plugin/revolution-addons/whiteboard/assets/images/write_right_angle.png',
	            width: 572,
	            height: 691,
	            handtype: 'right',
	            transform: {
	                transformX: 50,
	                transformY: 50
	            },
	            jittering: {
	                distance: '80',
	                distance_horizontal: '100',
	                repeat: '5',
	                offset: '10',
	                offset_horizontal: '0'
	            },
	            rotation:{
	                angle: '10',
	                repeat: '3'
	            }
	        }
	    },
	    addOnParticles: {
	    	enable: false
	    },
	    particles: {
			startSlide: "first", 
			endSlide: "last", 
			zIndex: "1",
			particles: {
				number: {value: 80}, color: {value: "#ffffff"},
				shape: {
					type: "circle", stroke: {width: 0, color: "#ffffff", opacity: 1},
					image: {src: ""}
				},
				opacity: {value: 0.5, random: true, min: 0.25, anim: {enable: false, speed: 3, opacity_min: 0, sync: false}},
				size: {value: 2, random: false, min: 30, anim: {enable: false, speed: 40, size_min: 1, sync: false}},
				line_linked: {enable: true, distance: 150, color: "#ffffff", opacity: 0.4, width: 1},
				move: {enable: true, speed: 6, direction: "none", random: true, min_speed: 6, straight: false, out_mode: "out"}
			},
			interactivity: {
				events: {onhover: {enable: false, mode: "repulse"}, onclick: {enable: false, mode: "repulse"}},
				modes: {grab: {distance: 400, line_linked: {opacity: 0.5}}, bubble: {distance: 400, size: 40, opacity: 0.4}, repulse: {distance: 200}}
			}
		},
		addOnCountdown: {
			enable: false,
			targetdate: new Date().getTime() + 864000000, // http://www.freeformatter.com/epoch-timestamp-to-date-converter.html
			slidechanges: [{days: 0, hours: 0, minutes: 0, seconds: 0, slide: 2}]
		},
		addOnSlicey: {
			enable: false
		},
		addOnFilmstrip: {
			enable: false
		},
		addOnBeforeAfter : {
			enable: false,
			options: {
				cursor: "move",
			    carousel: false,
			    arrowStyles: {
			        leftIcon: "fa-icon-caret-left",
			        rightIcon: "fa-icon-caret-right",
			        topIcon: "fa-icon-caret-up",
			        bottomIcon: "fa-icon-caret-down",
			        size: "35",
			        color: "#ffffff",
			        spacing: "10",
			        bgColor: "transparent",
			        padding: "0",
			        borderRadius: "0"
			    },
			    dividerStyles: {
			        width: "1",
			        color: "rgba(255, 255, 255, 0.5)"
			    }
			}
		},
		addOnPanorama: {
			enable: false
		},
		addOnRevealer: {
			enable: false,
		},
		revealer: {
			direction: "open_horizontal",
			color: "#ffffff",
			duration: "1500",
			delay: "0",
			easing: "Power2.easeInOut",
			overlay_enabled: true,
			overlay_color: "#000000",
			overlay_duration: "1500",
			overlay_delay: "0",
			overlay_easing: "Power2.easeInOut",
			spinner: "1",
			spinnerColor: "#006dd2",
			spinnerHtml: "<div class='rsaddon-revealer-spinner rsaddon-revealer-spinner-1'><div class='rsaddon-revealer-1'><span style='background: {{color}}'><\/span><span style='background: {{color}}'><\/span><span style='background: {{color}}'><\/span><span style='background: {{color}}'><\/span><span style='background: {{color}}'><\/span><span style='background: {{color}}'><\/span><span style='background: {{color}}'><\/span><span style='background: {{color}}'><\/span><span style='background: {{color}}'><\/span><span style='background: {{color}}'><\/span><\/div><\/div \/>"
		},
		addOnDuotone: {
			enable: false
		},
		addOnBubblemorph: {
			enable: false
		},
		addOnDistortion: {
			enable: false
		}
		
	};

	PluginRevolutionSlider.prototype = {
		initialize: function($el, opts) {
			if ($el.data(instanceName)) {
				return this;
			}

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build()
				.events();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginRevolutionSlider.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			if (!($.isFunction($.fn.revolution))) {
				return this;
			}

			// Single Slider Class
			if(this.options.wrapper.find('> ul > li').length == 1) {
				this.options.wrapper.addClass('slider-single-slide');

				// Remove Bullets
				// this.options.navigation.bullets.enable = false;
				$.extend(this.options.navigation, {
					bullets: {
						enable: false
					}
				});

			}

			// Full Screen Class
			if(this.options.sliderLayout == 'fullscreen') {
				this.options.wrapper.closest('.slider-container').addClass('fullscreen-slider');
			}
			
			// Initialize Revolution Slider
			this.options.wrapper.revolution(this.options);

			// Addon Init - Typewriter
			if(this.options.addOnTypewriter.enable) {
				RsTypewriterAddOn($, this.options.wrapper);
			}

			// Addon Init - Whiteboard
			if(this.options.addOnWhiteboard.enable) {
				this.options.wrapper.rsWhiteBoard();
			}

			// Addon Init - Particles
			if(this.options.addOnParticles.enable) {
				RsParticlesAddOn(this.options.wrapper);
			}

			// Addon Init - Countdown
			if(this.options.addOnCountdown.enable) {
				tp_countdown(this.options.wrapper, this.options.addOnCountdown.targetdate, this.options.addOnCountdown.slidechanges);
			}

			// Addon Init - Slicey
			if(this.options.addOnSlicey.enable) {
				this.options.wrapper.revSliderSlicey();
			}

			// Addon Init - Filmstrip
			if(this.options.addOnFilmstrip.enable) {
				RsFilmstripAddOn($, this.options.wrapper, '../vendor/rs-plugin/revolution-addons/filmstrip/', false);
			}

			// Addon Init - Before After
			if(this.options.addOnBeforeAfter.enable) {
				RevSliderBeforeAfter($, this.options.wrapper, this.options.addOnBeforeAfter.options);
			}

			// Addon Init - Panorama
			if(this.options.addOnPanorama.enable) {
				RsAddonPanorama($, this.options.wrapper);
			}

			// Addon Init - Revealer
			if(this.options.addOnRevealer.enable) {
				RsRevealerAddOn($, this.options.wrapper, this.options.revealer.spinnerHtml);
			}

			// Addon Init - Duotone
			if(this.options.addOnDuotone.enable) {
				RsAddonDuotone($, this.options.wrapper, true, "cubic-bezier(0.645, 0.045, 0.355, 1.000)", "1000");
			}

			// Addon Init - Bubblemorph
			if(this.options.addOnBubblemorph.enable) {
				BubbleMorphAddOn($, this.options.wrapper, false);
			}

			// Addon Init - Distortion
			if(this.options.addOnDistortion.enable) {
				RsLiquideffectAddOn($, this.options.wrapper);
			}

			return this;
		},

		events: function() {

			return this;
		}
	};

	// expose to scope
	$.extend(theme, {
		PluginRevolutionSlider: PluginRevolutionSlider
	});

	// jquery plugin
	$.fn.themePluginRevolutionSlider = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginRevolutionSlider($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// Scroll Spy
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__scrollSpy';

	var PluginScrollSpy = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginScrollSpy.defaults = {
		target: '#header',
		debugMode: false
	};

	PluginScrollSpy.prototype = {
		initialize: function($el, opts) {

			if( document.querySelector( opts.target ) == null ) {
				return false;
			}

			this.$el = $el;

			this
				.setData()
				.setOptions(opts);

			if( this.options.debugMode ) {
				this.debugMode();
			} 
			
			this.build();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginScrollSpy.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
            var self = this,
                target = document.querySelector( self.options.target ) != null ? document.querySelector( self.options.target ) : false,
                navItems = target == '#header' || target == '.wrapper-spy' ? target.querySelectorAll('.header-nav .nav > li a') : target.querySelectorAll('.nav > li a');

			// Get all section ID's
			var sectionIDs = Object.keys(navItems).map(function(key, index) {
				return navItems[key].hash;
			});

			// Remove empty values from sectionIDs array
			sectionIDs = sectionIDs.filter(function(value){
				return value != ''; 
			});

			// Store in a global variable
			self.sectionIDs = sectionIDs;

			for( var i = 0; i < sectionIDs.length; i++ ) {

				// Default Root Margin
				var rootMargin = '-20% 0px -79.9% 0px';
				
				// Spy Offset
				if( $( sectionIDs[i] ).data('spy-offset') ) {
					var rootMarginOffset = $( sectionIDs[i] ).data('spy-offset'),
						isNegativeOffset = parseInt( rootMarginOffset ) < 0 ? true : false;

					// Mount a new rootMargin based on offset value
					rootMargin = rootMargin.split(' ').map(function(element, index){
						if( element.indexOf('%') > 0 ) {
							var valueToInt = parseInt( element.replace('%','') ),
								newValue = 0;

							switch ( index ) {
								case 0:
									if( isNegativeOffset ) {
										newValue = valueToInt - rootMarginOffset;
									} else {
										newValue = Math.abs(valueToInt) + rootMarginOffset;
									}
									break;

								case 2:
									if( isNegativeOffset ) {
										newValue = valueToInt + rootMarginOffset;
									} else {
										newValue = Math.abs(valueToInt) - rootMarginOffset;
									}
									break;
							
							}

							if( isNegativeOffset ) {
								newValue = newValue + '%';
							} else {
								newValue = '-' + newValue + '%';
							}

							return newValue;
						} else {
							return element;
						}
					}).join(' ');
				}

				var selector = sectionIDs[i],
					callback = function() {
						var $section = $(this);

						if( target == '#header' || target == '.wrapper-spy' ) {
							$('#header .header-nav .nav > li a').removeClass('active');
							$('#header .header-nav .nav > li a[href="#'+ $section[0].id +'"]').addClass('active');
						} else {
							$( target ).find('.nav > li a').removeClass('active');
							$( target ).find('.nav > li a[href="#'+ $section[0].id +'"]').addClass('active');
						}
						
					}

				this.scrollSpyIntObs( selector, callback, { 
					rootMargin: rootMargin,
					threshold: 0
				}, true, i, true);

            }

            return this;

		},

		scrollSpyIntObs: function(selector, functionName, intObsOptions, alwaysObserve, index, firstLoad) {
			var self = this;

			var $el = document.querySelectorAll( selector );
			var intersectionObserverOptions = {
				rootMargin: '0px 0px 200px 0px'
			}

			if( Object.keys(intObsOptions).length ) {
				intersectionObserverOptions = $.extend(intersectionObserverOptions, intObsOptions);
			}

			var observer = new IntersectionObserver(function(entries) {

				for(var i=0; i < entries.length; i++) {
					var entry = entries[i];

					if (entry.intersectionRatio > 0 ) {
						if( typeof functionName === 'string' ) {
							var func = Function( 'return ' + functionName )();
						} else {
							var callback = functionName;

							callback.call( $(entry.target) );
						}

						// Unobserve
						if( !alwaysObserve ) {
							observer.unobserve(entry.target);   
						}

					} else {
						if( firstLoad == false ) {
							if( index == self.sectionIDs.length - 1 ) {
								$('#header .header-nav .nav > li a').removeClass('active');
								$('#header .header-nav .nav > li a[href="#'+ entry.target.id +'"]').parent().prev().find('a').addClass('active');
							}
						}
						firstLoad = false;

					}
				}
			}, intersectionObserverOptions);
			
			$( $el ).each(function(){
				observer.observe( $(this)[0] );
			});

			return this;
		},

		debugMode: function() {
			function wrapCallback(cb) {
				return (...args) => wrapper(cb, ...args)
			}

			function addFlashingRect(bounds, style = {}, entry) {
				const { width, left, height, top } = bounds
				const div = document.createElement("div")
				div.style.position = "fixed"
				div.style.width = width + "px"
				div.style.left = left + "px"
				div.style.top = top + "px"
				div.style.height = height + "px"
				div.style.pointerEvents = "none"
				div.style.transition = "opacity 2s ease-in"
				div.style.zIndex = 9999999999;
				Object.assign(div.style, style)
				requestAnimationFrame(() =>
					requestAnimationFrame(() => {
					div.style.opacity = 0
				})
				)
				div.addEventListener("transitionend", () => {
					document.body.removeChild(div)
				})
				
				document.body.appendChild(div)

				if( entry ) {
					var newdiv = document.createElement("div");
					
					newdiv.style.backgroundColor = '#000';
					newdiv.style.color = '#FFF';
					newdiv.style.paddingTop = '10px';
					newdiv.style.paddingRight = '10px';
					newdiv.style.paddingLeft = '10px';
					newdiv.style.paddingBottom = '10px';
					newdiv.innerHTML = entry.target.id;
					div.appendChild( newdiv );
				}
				
				return div
			}

			const iodOptions = {
				rootColor: "#9428AB",
				enterColor: "#B35C00",
				exitColor: "#035570",
				interColor: "#9CAF00BB",
			}

			function showEntry(entry) {
				addFlashingRect(entry.rootBounds, {
					border: `${Math.min(10, entry.rootBounds.height / 2)}px solid ${
					iodOptions.rootColor
					}`,
					overflow: "hidden",
					boxSizing: "border-box",
				}, entry);
			}

			function wrapper(cb, entries, observer) {
				entries.forEach(showEntry)
				return cb(entries, observer)
			}

			if (typeof window != "undefined") {
				window.IntersectionObserver = class extends IntersectionObserver {
					constructor(cb, o) {
						super(wrapCallback(cb), o)
					}
				}
			}

			return this;
		}
	};

	// expose to scope
	$.extend(theme, {
		PluginScrollSpy: PluginScrollSpy
	});

	// jquery plugin
	$.fn.themePluginScrollSpy = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginScrollSpy($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// Scroll to Top
(function(theme, $) {

	theme = theme || {};

	$.extend(theme, {

		PluginScrollToTop: {

			defaults: {
				wrapper: $('body'),
				offset: 150,
				buttonClass: 'scroll-to-top',
				iconClass: 'fas fa-chevron-up',
				delay: 1000,
				visibleMobile: false,
				label: false,
				easing: 'easeOutBack'
			},

			initialize: function(opts) {
				initialized = true;

				// Don't initialize if the page has Section Scroll
				if( $('body[data-plugin-section-scroll]').get(0) ) {
					return;
				}

				this
					.setOptions(opts)
					.build()
					.events();

				return this;
			},

			setOptions: function(opts) {
				this.options = $.extend(true, {}, this.defaults, opts);

				return this;
			},

			build: function() {
				var self = this,
					$el;

				// Base HTML Markup
				$el = $('<a />')
					.addClass(self.options.buttonClass)
					.attr({
						'href': '#',
					})
					.append(
						$('<i />')
						.addClass(self.options.iconClass)
				);

				// Visible Mobile
				if (!self.options.visibleMobile) {
					$el.addClass('hidden-mobile');
				}

				// Label
				if (self.options.label) {
					$el.append(
						$('<span />').html(self.options.label)
					);
				}

				this.options.wrapper.append($el);

				this.$el = $el;

				return this;
			},

			events: function() {
				var self = this,
					_isScrolling = false;

				// Click Element Action
				self.$el.on('click', function(e) {
					e.preventDefault();
					$('html').animate({
						scrollTop: 0
					}, self.options.delay, self.options.easing);
					return false;
				});

				// Show/Hide Button on Window Scroll event.
				$(window).scroll(function() {

					if (!_isScrolling) {

						_isScrolling = true;

						if ($(window).scrollTop() > self.options.offset) {

							self.$el.stop(true, true).addClass('visible');
							_isScrolling = false;

						} else {

							self.$el.stop(true, true).removeClass('visible');
							_isScrolling = false;

						}

					}

				});

				return this;
			}

		}

	});

}).apply(this, [window.theme, jQuery]);

// Scrollable
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__scrollable';

	var PluginScrollable = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginScrollable.updateModals = function() {
		PluginScrollable.updateBootstrapModal();
	};

	PluginScrollable.updateBootstrapModal = function() {
		var updateBoostrapModal;

		updateBoostrapModal = typeof $.fn.modal !== 'undefined';
		updateBoostrapModal = updateBoostrapModal && typeof $.fn.modal.Constructor !== 'undefined';
		updateBoostrapModal = updateBoostrapModal && typeof $.fn.modal.Constructor.prototype !== 'undefined';
		updateBoostrapModal = updateBoostrapModal && typeof $.fn.modal.Constructor.prototype.enforceFocus !== 'undefined';

		if ( !updateBoostrapModal ) {
			return false;
		}

		var originalFocus = $.fn.modal.Constructor.prototype.enforceFocus;
		$.fn.modal.Constructor.prototype.enforceFocus = function() {
			originalFocus.apply( this );

			var $scrollable = this.$element.find('.scrollable');
			if ( $scrollable ) {
				if ( $.isFunction($.fn['themePluginScrollable'])  ) {
					$scrollable.themePluginScrollable();
				}

				if ( $.isFunction($.fn['nanoScroller']) ) {
					$scrollable.nanoScroller();
				}
			}
		};
	};

	PluginScrollable.defaults = {
		contentClass: 'scrollable-content',
		paneClass: 'scrollable-pane',
		sliderClass: 'scrollable-slider',
		alwaysVisible: true,
		preventPageScrolling: true
	};

	PluginScrollable.prototype = {
		initialize: function($el, opts) {
			if ( $el.data( instanceName ) ) {
				return this;
			}

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginScrollable.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			this.options.wrapper.nanoScroller(this.options);

			return this;
		}
	};

	// expose to scope
	$.extend(theme, {
		PluginScrollable: PluginScrollable
	});

	// jquery plugin
	$.fn.themePluginScrollable = function(opts) {
		return this.each(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginScrollable($this, opts);
			}

		});
	};

	$(function() {
		PluginScrollable.updateModals();
	});

}).apply(this, [window.theme, jQuery]);

// Section Scroll
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__sectionScroll';

	var PluginSectionScroll = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginSectionScroll.defaults = {
		targetClass: '.section',
		dotsNav: true,
		changeHeaderLogo: true,
		headerLogoDark: 'img/logo-default-slim.png',
		headerLogoLight: 'img/logo-default-slim-dark.png'
	};

	PluginSectionScroll.prototype = {
		initialize: function($el, opts) {
			if ($el.data(instanceName)) {
				return this;
			}

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build()
				.events();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginSectionScroll.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			var self = this,
				$el = this.options.wrapper;

			// Check type of header and change the target for header (by change header color purpose)
			if( $('html').hasClass('side-header-overlay-full-screen') ) {
				self.$header = $('.sticky-wrapper');
			} else {
				self.$header = $('#header');
			}

			// Turn the section full height or not depeding on the content size
			self.updateSectionsHeight();

			// Wrap all sections in a section wrapper
			$( this.options.targetClass ).wrap('<div class="section-wrapper"></div>');

			// Set the section wrapper height
	  		$('.section-wrapper').each(function(){
	  			$(this).height( $(this).find('.section-scroll').outerHeight() );
	  		});

	  		// Add active class to the first section on page load
	  		$('.section-wrapper').first().addClass('active');
			
	        var	flag = false,
	            scrollableFlag = false,
	        	touchDirection = '',
	        	touchstartY = 0,
	        	touchendY = 0;

	        $(window).on('touchstart', function(event) {
			    touchstartY = event.changedTouches[0].screenY;
			});

	        var wheelEvent = 'onwheel' in document ? 'wheel' : document.onmousewheel !== undefined ? 'mousewheel' : 'DOMMouseScroll';
	        if( $(window).width() < 992 && $('html').hasClass('touch') ) {
	        	wheelEvent = 'onwheel' in document ? 'wheel touchend' : document.onmousewheel !== undefined ? 'mousewheel touchend' : 'DOMMouseScroll touchend';
	        }

        	if( $(window).width() < 992 ) {
	    		$('html').removeClass('overflow-hidden');
			    $(window).on('scroll', function(){

		    		var index = 0;
		    		$('.section-scroll').each(function(){
		    			if( $(this).offset().top <= $(window).scrollTop() + 50 ) {
		    				var $currentSection2 = $('.section-wrapper').eq( index ).find('.section-scroll');

			            	$('.section-scroll-dots-navigation > ul > li').removeClass('active');
							$('.section-scroll-dots-navigation > ul > li').eq( index ).addClass('active');

							$(window).trigger({
								type: 'section.scroll.mobile.change.header.color',
								currentSection: $currentSection2
							});
		    			}

		    			index++;
		    		});
		    		
			    });

			    $(window).on('section.scroll.mobile.change.header.color', function(e){
			    	if( typeof e.currentSection == 'undefined' ) {
			    		return;
			    	}

			    	var $currentSection = e.currentSection,
			    		headerColor     = $currentSection.data('section-scroll-header-color');
								    	
			    	$('#header .header-nav').removeClass('header-nav-light-text header-nav-dark-text').addClass('header-nav-' + headerColor + '-text');
			    	$('#header .header-nav-features').removeClass('header-nav-features-dark header-nav-features-light').addClass('header-nav-features-' + headerColor);
			    	$('#header .header-social-icons').removeClass('social-icons-icon-dark social-icons-icon-light').addClass('social-icons-icon-' + headerColor);

			    	// Change Logo
			    	if( self.options.changeHeaderLogo && headerColor != undefined ) {
				    	if( headerColor == 'light' ) {
				    		$('#header .header-logo img').attr('src', self.options.headerLogoLight);
				    	} else if( headerColor == 'dark' ) {
				    		$('#header .header-logo img').attr('src', self.options.headerLogoDark);
				    	}
			    	}

			    	self.$header.css({
			    		opacity: 1
			    	});

			    });
        	}

	        $(window).on(wheelEvent, function(e){
	        	if( $(window).width() < 992 ) {
	        		return;
	        	}

	        	if( $(window).width() < 992 && $('html').hasClass('touch') ) {
		        	if( $(e.target).closest('.section-scroll-dots-navigation').get(0) || $(e.target).closest('.header-body').get(0) || $(e.target).closest('.owl-carousel').get(0) ) {
		        		return;
		        	}
		        }

		        // Side Header Overlay Full Screen
		        if( $('html.side-header-overlay-full-screen.side-header-hide').get(0) ) {
		        	return;
		        }

	        	var wheelDirection = e.originalEvent.wheelDelta == undefined ? e.originalEvent.deltaY > 0 : e.originalEvent.wheelDelta < 0;
	        	if( $(window).width() < 992 && $('html').hasClass('touch') ) {
		        	touchendY = event.changedTouches[0].screenY;
	        		
				    if( touchendY <= touchstartY ) {
				    	touchDirection = 'up';
				    }

				    if( touchendY >= touchstartY ) {
				    	touchDirection = 'down';
				    }

				    if( touchendY == touchstartY ) {
				    	return;
				    }
	        	}

	        	var $currentSection = $('.section-wrapper').eq( self.getCurrentIndex() ).find('.section-scroll'),
            		$nextSection = self.getNextSection(wheelDirection, touchDirection),
            		nextSectionOffsetTop;

            	// If is the last section, then change the offsetTop value
            	if( self.getCurrentIndex() == $('.section-wrapper').length - 1 ) {
            		nextSectionOffsetTop = $(document).height();
            	} else {
            		nextSectionOffsetTop = $nextSection.offset().top;
            	}

			    if( $(window).width() < 992 && $('html').hasClass('touch') ) {
				    setTimeout(function(){
					    if( $('.section-wrapper').eq( self.getCurrentIndex() ).find('.section-scroll').hasClass('section-scroll-scrollable') ) {
					    	$('html').removeClass('overflow-hidden');
					    } else {
					    	$('html').addClass('overflow-hidden');
					    }
				    }, 1200);
				}

	        	// For non full height sections
	        	if( $currentSection.hasClass('section-scroll-scrollable') ) {
	        		if( !flag && !scrollableFlag ) {

		        		// Scroll Direction
		        		if(wheelDirection || touchDirection == 'up') {
		        			if( ( $(window).scrollTop() + $(window).height() ) >= nextSectionOffsetTop ) {
		        				flag = true;
								setTimeout(function(){
									$(window).trigger('section.scroll.change.header.color');

					            	setTimeout(function(){
										flag = false;
									}, 500);
								}, 1000);

		        				if( self.getCurrentIndex() == ( $('.section-wrapper').length - 1 )  ) {
						    		return false;
						    	}

		        				// Move to the next section
		        				self.moveTo( $currentSection.offset().top + $currentSection.outerHeight() );

		        				// Change Section Active Class
					   			self.changeSectionActiveState( $nextSection );

					   			self.$header.css({
							    	opacity: 0,
							    	transition: 'ease opacity 500ms'
							    });
					        }

			        		if( !$('html').hasClass('touch') ) {
				        		for( var i = 1; i < 100; i++ ) {
					        		$('body, html').scrollTop( $(window).scrollTop() + 1 );

					        		if( ( $(window).scrollTop() + $(window).height() ) >= nextSectionOffsetTop ) {
					        			scrollableFlag = true;
										setTimeout(function(){
											$(window).trigger('section.scroll.change.header.color');
							            	scrollableFlag = false;
										}, 500);
					        			break;
					        		}
				        		}
				        	}
					    } else {
					    	if( $(window).scrollTop() <= $currentSection.offset().top ) {
					    		flag = true;
								setTimeout(function(){
									$(window).trigger('section.scroll.change.header.color');

					            	setTimeout(function(){
										flag = false;
									}, 500);
								}, 1000);

					    		if( self.getCurrentIndex() == 0  ) {
						    		return false;
						    	}

					   			// Move to the next section
		        				self.moveTo( $currentSection.offset().top - $(window).height() );

		        				// Change Section Active Class
					   			self.changeSectionActiveState( $nextSection );

					   			self.$header.css({
							    	opacity: 0,
							    	transition: 'ease opacity 500ms'
							    });
					        }

					    	if( !$('html').hasClass('touch') ) {
				        		for( var i = 1; i < 100; i++ ) {
					        		$('body, html').scrollTop( $(window).scrollTop() - 1 );

					        		if( $(window).scrollTop() <= $currentSection.offset().top ) {
					        			scrollableFlag = true;
										setTimeout(function(){
											$(window).trigger('section.scroll.change.header.color');
							            	scrollableFlag = false;
										}, 500);
					        			break;
					        		}
				        		}
				        	}
					    }

			   			// Change Dots Active Class
				        self.changeDotsActiveState();

		        		return;

		        	}
	        	}

	        	// For full height sections
	        	if( !flag && !scrollableFlag ) {
				    if(wheelDirection || touchDirection == 'up') {
				    	if( self.getCurrentIndex() == ( $('.section-wrapper').length - 1 )  ) {
				    		return false;
				    	}

				   		// Change Section Active Class
			   			self.changeSectionActiveState( $nextSection );

				   		setTimeout(function(){
				   			// Move to the next section
	        				self.moveTo( $nextSection.offset().top );

				   		}, 150);
				    } else {
				    	if( self.getCurrentIndex() == 0  ) {
				    		return false;
				    	}

				   		// Change Section Active Class
			   			self.changeSectionActiveState( $nextSection );

				   		if( $nextSection.height() > $(window).height() ) {
				   			// Move to the next section
	        				self.moveTo( $currentSection.offset().top - $(window).height() );
				   		} else {
					        setTimeout(function(){
					   			// Move to the next section
		        				self.moveTo( $nextSection.offset().top );

					   		}, 150);
				   		}
				    }

				    // Change Dots Active Class
			        self.changeDotsActiveState();

				    self.$header.css({
				    	opacity: 0,
				    	transition: 'ease opacity 500ms'
				    });

				    // Style next section
	            	$nextSection.css({
	            		position: 'relative',
	            		opacity: 1,
	            		'z-index': 1,
	            		transform: 'translate3d(0,0,0) scale(1)'
	            	});

	            	// Style previous section
	            	$currentSection.css({
	            		position: 'fixed',
	            		width: '100%',
	            		top: 0,
	            		left: 0,
	            		opacity: 0,
	            		'z-index': 0,
	            		transform: 'translate3d(0,0,-10px) scale(0.7)',
	            		transition: 'ease transform 600ms, ease opacity 600ms',
	            	});

					setTimeout(function(){
						$currentSection.css({
		            		position: 'relative',
		            		opacity: 1,
		            		transform: 'translate3d(0,0,-10px) scale(1)'
		            	});

						$(window).trigger('section.scroll.change.header.color');

		            	setTimeout(function(){
							flag = false;
						}, 500);
					}, 1000);

					flag = true;

				}

				return;
	        });

	        // Dots Navigation
	        if( this.options.dotsNav ) {
	        	self.dotsNavigation();
	        }

	        // First Load
	        setTimeout(function(){
		        if( $(window.location.hash).get(0) ) {
		        	self.moveTo( $(window.location.hash).parent().offset().top );

		        	self.changeSectionActiveState( $(window.location.hash) );

		        	// Change Dots Active Class
			        self.changeDotsActiveState();

		        	self.updateHash( true );
		        } else {
		        	var hash  = window.location.hash,
		        		index = hash.replace('#','');

		        	if( !hash ) {
		        		index = 1;
		        	}

		        	self.moveTo( $('.section-wrapper').eq( index - 1 ).offset().top );

		        	self.changeSectionActiveState( $('.section-wrapper').eq( index - 1 ).find('.section-scroll') );

		        	// Change Dots Active Class
			        self.changeDotsActiveState();

		        	self.updateHash( true );
		        }

				$(window).trigger('section.scroll.ready');
				$(window).trigger('section.scroll.change.header.color');
			}, 500);

			return this;
		},

		updateSectionsHeight: function() {
			var self = this;

			$('.section-scroll').css({ height: '' });

			$('.section-scroll').each(function(){
				if( $(this).outerHeight() < ( $(window).height() + 3 ) ) {
					$(this).css({ height: '100vh' });		
				} else {
					$(this).addClass('section-scroll-scrollable');
				}
			});

			// Set the section wrapper height
	  		$('.section-wrapper').each(function(){
	  			$(this).height( $(this).find('.section-scroll').outerHeight() );
	  		});

			return this;
		},

		updateHash: function( first_load ){
			var self = this;

			if( !window.location.hash ) {
				window.location.hash = 1;
			} else {
				if(!first_load) {
					var $section = $('.section-wrapper').eq( self.getCurrentIndex() ).find('.section-scroll'),
						section_id = $section.attr('id') ? $section.attr('id') : $section.parent().index() + 1;

					window.location.hash = section_id;
				}
			}

			return this;
		},

		getCurrentIndex: function() {
			var self = this,
				currentIndex = 0;

			currentIndex = $('.section-wrapper.active').index();

			return currentIndex;
		},

		moveTo: function( $scrollTopValue, first_load ) {
			var self = this;

			$('body, html').animate({
   				scrollTop: $scrollTopValue
   			}, 1000, 'easeOutQuint');

   			setTimeout(function(){
	   			self.updateHash();
   			}, 500);

			return this;
		},

		getNextSection: function(wheelDirection, touchDirection) {
			var self = this,
				$nextSection = '';

			// Scroll Direction
        	if(wheelDirection || touchDirection == 'up') {
				$nextSection = $('.section-wrapper').eq( self.getCurrentIndex() + 1 ).find('.section-scroll');
        	} else {
        		$nextSection = $('.section-wrapper').eq( self.getCurrentIndex() - 1 ).find('.section-scroll');
        	}

			return $nextSection;
		},

		changeSectionActiveState: function( $nextSection ) {
			var self = this;

			$('.section-wrapper').removeClass('active');
	   		$nextSection.parent().addClass('active');

			return this;
		},

		changeDotsActiveState: function() {
			var self = this;

			$('.section-scroll-dots-navigation > ul > li').removeClass('active');
			$('.section-scroll-dots-navigation > ul > li').eq( self.getCurrentIndex() ).addClass('active');

			return this;
		},

		dotsNavigation: function() {
			var self = this;

			var dotsNav = $('<div class="section-scroll-dots-navigation"><ul class="list list-unstyled"></ul></div>'),
        		currentSectionIndex = self.getCurrentIndex();

        	if( self.options.dotsClass ) {
        		dotsNav.addClass( self.options.dotsClass );
        	}

        	for( var i = 0; i < $('.section-scroll').length; i++ ) {
        		var title = $('.section-wrapper').eq( i ).find('.section-scroll').data('section-scroll-title');

        		dotsNav.find('> ul').append( '<li'+ ( ( currentSectionIndex == i ) ? ' class="active"' : '' ) +'><a href="#'+ i +'" data-nav-id="'+ i +'"><span>'+ title +'</span></a></li>' );
        	}

        	$('.body').append( dotsNav );

        	dotsNav.find('a[data-nav-id]').on('click touchstart', function(e){
        		e.preventDefault();
        		var $this = $(this);

        		$('.section-scroll').css({
        			opacity: 0,
        			transition: 'ease opacity 300ms'
        		});

        		self.$header.css({
			    	opacity: 0,
			    	transition: 'ease opacity 500ms'
			    });

        		setTimeout(function(){
	        		self.moveTo( $('.section-wrapper').eq( $this.data('nav-id') ).offset().top )

		   			$('.section-wrapper').removeClass('active');
			   		$('.section-wrapper').eq( $this.data('nav-id') ).addClass('active');

	        		$('.section-wrapper').eq( self.getCurrentIndex() ).find('.section-scroll').css({
	        			opacity: 1
	        		});

	        		setTimeout(function(){
		        		$('.section-scroll').css({ opacity: 1 });

		        		$(window).trigger('section.scroll.change.header.color');
	        		}, 500);

	        		if( $(window).width() > 991 ) {
		        		self.changeDotsActiveState();
	        		}
        		}, 500);
        	});

			return this;
		},

		events: function() {
			var self = this;

			$(window).on('section.scroll.ready', function(){
				$(window).scrollTop(0);
			});

			$(window).on('section.scroll.change.header.color', function(){
		    	var headerColor = $('.section-wrapper').eq( self.getCurrentIndex() ).find('.section-scroll').data('section-scroll-header-color');
		    	
		    	$('#header .header-nav').removeClass('header-nav-light-text header-nav-dark-text').addClass('header-nav-' + headerColor + '-text');
		    	$('#header .header-nav-features').removeClass('header-nav-features-dark header-nav-features-light').addClass('header-nav-features-' + headerColor);
		    	$('#header .header-social-icons').removeClass('social-icons-icon-dark social-icons-icon-light').addClass('social-icons-icon-' + headerColor);

		    	// Change Logo
		    	if( self.options.changeHeaderLogo && headerColor != undefined ) {
			    	if( headerColor == 'light' ) {
			    		$('#header .header-logo img').attr('src', self.options.headerLogoLight);
			    	} else if( headerColor == 'dark' ) {
			    		$('#header .header-logo img').attr('src', self.options.headerLogoDark);
			    	}
		    	}

		    	self.$header.css({
		    		opacity: 1
		    	});
		    });

			$(document).ready(function(){
			    $(window).afterResize(function(){
			    	self.updateSectionsHeight();

			    	if( $(window).width() < 992 ) {
			    		$('html').removeClass('overflow-hidden');
			    	}
			    });
			});

		    return this;
		}
	};

	// expose to scope
	$.extend(theme, {
		PluginSectionScroll: PluginSectionScroll
	});

	// jquery plugin
	$.fn.themePluginSectionScroll = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginSectionScroll($this, opts);
			}

		});
	};

}).apply(this, [window.theme, jQuery]);

// Sort
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__sort';

	var PluginSort = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginSort.defaults = {
		useHash: true,
		itemSelector: '.isotope-item',
		layoutMode: 'masonry',
		filter: '*',
		hiddenStyle: {
			opacity: 0
		},
		visibleStyle: {
			opacity: 1
		},
		stagger: 30,
		isOriginLeft: ($('html').attr('dir') == 'rtl' ? false : true)
	};

	PluginSort.prototype = {
		initialize: function($el, opts) {
			if ($el.data(instanceName)) {
				return this;
			}

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginSort.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			if (!($.isFunction($.fn.isotope))) {
				return this;
			}

			var self = this,
				$source = this.options.wrapper,
				$destination = $('.sort-destination[data-sort-id="' + $source.attr('data-sort-id') + '"]'),
				$window = $(window);

			if ($destination.get(0)) {

				self.$source = $source;
				self.$destination = $destination;
				self.$loader = false;

				self.setParagraphHeight($destination);

				if (self.$destination.parents('.sort-destination-loader').get(0)) {
					self.$loader = self.$destination.parents('.sort-destination-loader');
					self.createLoader();
				}

				$destination.attr('data-filter', '*');

				$destination.one('layoutComplete', function(event, laidOutItems) {
					self.removeLoader();

					// If has data-plugin-sticky on the page we need recalculate sticky position
					if( $('[data-plugin-sticky]').length ) {
						setTimeout(function(){
							$('[data-plugin-sticky]').each(function(){
								$(this).data('__sticky').build();
								$(window).trigger('resize');
							});
						}, 500);
					}
				});

				// IE10/11 fix
				if( $('html').hasClass('ie10') || $('html').hasClass('ie11') ) {
					var padding = parseInt( self.options.wrapper.children().css('padding-left') ) + parseInt( self.options.wrapper.children().css('padding-right') );
				}

				$destination.waitForImages(function() {
					$destination.isotope(self.options);
					self.events();
				});


				setTimeout(function() {
					self.removeLoader();
				}, 3000);

			}

			return this;
		},

		events: function() {
			var self = this,
				filter = null,
				$window = $(window);

			self.$source.find('a').click(function(e) {
				e.preventDefault();

				filter = $(this).parent().data('option-value');

				self.setFilter(filter);

				if (e.originalEvent) {
					self.$source.trigger('filtered');
				}

				return this;
			});

			self.$destination.trigger('filtered');
			self.$source.trigger('filtered');

			if (self.options.useHash) {
				self.hashEvents();
			}

			$window.on('resize sort.resize', function() {
				setTimeout(function() {
					self.$destination.isotope('layout');
				}, 300);
			});

			setTimeout(function() {
				$window.trigger('sort.resize');
			}, 300);

			return this;
		},

		setFilter: function(filter) {
			var self = this,
				page = false,
				currentFilter = filter;

			self.$source.find('.active').removeClass('active');
			self.$source.find('li[data-option-value="' + filter + '"], li[data-option-value="' + filter + '"] > a').addClass('active');

			self.options.filter = currentFilter;

			if (self.$destination.attr('data-current-page')) {
				currentFilter = currentFilter + '[data-page-rel=' + self.$destination.attr('data-current-page') + ']';
			}

			self.$destination.attr('data-filter', filter).isotope({
				filter: currentFilter
			}).one('arrangeComplete', function( event, filteredItems ) {
				
				if (self.options.useHash) {
					if (window.location.hash != '' || self.options.filter.replace('.', '') != '*') {
						window.location.hash = self.options.filter.replace('.', '');
					}
				}
				
				$(window).trigger('scroll');

			}).trigger('filtered');

			return this;
		},

		hashEvents: function() {
			var self = this,
				hash = null,
				hashFilter = null,
				initHashFilter = '.' + location.hash.replace('#', '');

			// Check if has scroll to section trough URL hash and prevent the sort plugin from show nothing
			if( $(location.hash).length ) {
				initHashFilter = '.';
			}

			if (initHashFilter != '.' && initHashFilter != '.*') {
				self.setFilter(initHashFilter);
			}

			$(window).on('hashchange', function(e) {

				hashFilter = '.' + location.hash.replace('#', '');
				hash = (hashFilter == '.' || hashFilter == '.*' ? '*' : hashFilter);

				self.setFilter(hash);

			});

			return this;
		},

		setParagraphHeight: function() {
			var self = this,
				minParagraphHeight = 0,
				paragraphs = $('span.thumb-info-caption p', self.$destination);

			paragraphs.each(function() {
				if ($(this).height() > minParagraphHeight) {
					minParagraphHeight = ($(this).height() + 10);
				}
			});

			paragraphs.height(minParagraphHeight);

			return this;
		},

		createLoader: function() {
			var self = this;

			var loaderTemplate = [
				'<div class="bounce-loader">',
					'<div class="bounce1"></div>',
					'<div class="bounce2"></div>',
					'<div class="bounce3"></div>',
				'</div>'
			].join('');

			self.$loader.append(loaderTemplate);

			return this;
		},

		removeLoader: function() {

			var self = this;

			if (self.$loader) {

				self.$loader.removeClass('sort-destination-loader-showing');

				setTimeout(function() {
					self.$loader.addClass('sort-destination-loader-loaded');
				}, 300);

			}

		}

	};

	// expose to scope
	$.extend(theme, {
		PluginSort: PluginSort
	});

	// jquery plugin
	$.fn.themePluginSort = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginSort($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// Star Rating
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__starrating';

	var PluginStarRating = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginStarRating.defaults = {
		theme: 'krajee-fas',
		color: 'primary',
		showClear: false,
		showCaption: false
	};

	PluginStarRating.prototype = {
		initialize: function($el, opts) {
			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginStarRating.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {

			if (!($.isFunction($.fn.rating))) {
				return this;
			}

			var self = this;

			self.options.wrapper
				.rating(self.options);

			self.options.wrapper.parents('.rating-container')
				.addClass('rating-' + self.options.color);

			if( self.options.extraClass ) {
				self.options.wrapper.parents('.rating-container')
					.addClass(self.options.extraClass);
			}

			return this;

		}
	};

	// expose to scope
	$.extend(theme, {
		PluginStarRating: PluginStarRating
	});

	// jquery plugin
	$.fn.themePluginStarRating = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginStarRating($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// Sticky
(function(theme, $) {
	
	theme = theme || {};
	
	var instanceName = '__sticky';

	var PluginSticky = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginSticky.defaults = {
		minWidth: 991,
		activeClass: 'sticky-active'
	};

	PluginSticky.prototype = {
		initialize: function($el, opts) {
			if ( $el.data( instanceName ) ) {
				return this;
			}

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build()
				.events();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginSticky.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			if (!($.isFunction($.fn.pin))) {
				return this;
			}

			var self = this,
				$window = $(window);
			
			self.options.wrapper.pin(self.options);

			if( self.options.wrapper.hasClass('sticky-wrapper-transparent') ) {
				self.options.wrapper.parent().addClass('position-absolute w-100');
			}

			$window.afterResize(function() {
				self.options.wrapper.removeAttr('style').removeData('pin');
				self.options.wrapper.pin(self.options);
				$window.trigger('scroll');
			});

			// Change Logo Src
			if( self.options.wrapper.find('img').attr('data-change-src') ) {
				var $logo      = self.options.wrapper.find('img'),
					logoSrc    = $logo.attr('src'),
					logoNewSrc = $logo.attr('data-change-src');

				self.changeLogoSrc = function(activate) {
					if(activate) {
						$logo.attr('src', logoNewSrc);
					} else {
						$logo.attr('src', logoSrc);
					}
				}
			}
			
			return this;
		},

		events: function() {
			var self = this,
				$window = $(window),
				$logo = self.options.wrapper.find('img'),
				sticky_activate_flag = true,
				sticky_deactivate_flag = false,
				class_to_check = ( self.options.wrapper.hasClass('sticky-wrapper-effect-1') ) ? 'sticky-effect-active' : 'sticky-active';

			$window.on('scroll sticky.effect.active', function(){
				if( self.options.wrapper.hasClass( class_to_check ) ) {		
					if( sticky_activate_flag ) {			
						if( $logo.attr('data-change-src') ) {
							self.changeLogoSrc(true);
						}

						sticky_activate_flag = false;
						sticky_deactivate_flag = true;
					}
				} else {	
					if( sticky_deactivate_flag ) {				
						if( $logo.attr('data-change-src') ) {
							self.changeLogoSrc(false);
						}

						sticky_deactivate_flag = false;
						sticky_activate_flag = true;
					}
				}
			});

			var is_backing = false;
			if( self.options.stickyStartEffectAt ) {

				// First Load
				if( self.options.stickyStartEffectAt < $window.scrollTop() ) {
					self.options.wrapper.addClass('sticky-effect-active');

					$window.trigger('sticky.effect.active');
				}

				$window.on('scroll', function(){
					if( self.options.stickyStartEffectAt < $window.scrollTop() ) {	
						self.options.wrapper.addClass('sticky-effect-active');
						is_backing = true;

						$window.trigger('sticky.effect.active');
					} else {	
						if( is_backing ) {
							self.options.wrapper.find('.sticky-body').addClass('position-fixed');
							is_backing = false;
						}

						if( $window.scrollTop() == 0 ) {
							self.options.wrapper.find('.sticky-body').removeClass('position-fixed');
						}

						self.options.wrapper.removeClass('sticky-effect-active');
					}
				});
			}

			// Refresh Sticky Plugin if click in a data-toggle="collapse"
			if( $('[data-bs-toggle="collapse"]').get(0) ) {

				$('[data-bs-toggle="collapse"]').on('click', function(){
					setTimeout(function(){
						self.build();
						$(window).trigger('scroll');
					}, 1000);
				});

			}
		}
	};

	// expose to scope
	$.extend(theme, {
		PluginSticky: PluginSticky
	});

	// jquery plugin
	$.fn.themePluginSticky = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginSticky($this, opts);
			}
			
		});
	}

}).apply(this, [ window.theme, jQuery ]);

// Toggle
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__toggle';

	var PluginToggle = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginToggle.defaults = {
		duration: 350,
		isAccordion: false
	};

	PluginToggle.prototype = {
		initialize: function($el, opts) {
			if ($el.data(instanceName)) {
				return this;
			}

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginToggle.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			var self = this,
				$wrapper = this.options.wrapper,
				$items = $wrapper.find('> .toggle'),
				$el = null;

			$items.each(function() {
				$el = $(this);

				if ($el.hasClass('active')) {
					$el.find('> p').addClass('preview-active');
					$el.find('> .toggle-content').slideDown(self.options.duration);
				}

				self.events($el);
			});

			if (self.options.isAccordion) {
				self.options.duration = self.options.duration / 2;
			}

			return this;
		},

		events: function($el) {
			var self = this,
				previewParCurrentHeight = 0,
				previewParAnimateHeight = 0,
				toggleContent = null;

			$el.find('> label, > .toggle-title').click(function(e) {

				var $this = $(this),
					parentSection = $this.parent(),
					parentWrapper = $this.parents('.toggle'),
					previewPar = null,
					closeElement = null;

				if (self.options.isAccordion && typeof(e.originalEvent) != 'undefined') {
					closeElement = parentWrapper.find('.toggle.active > label, .toggle.active > .toggle-title');

					if (closeElement[0] == $this[0]) {
						return;
					}
				}

				parentSection.toggleClass('active');

				// Preview Paragraph
				if (parentSection.find('> p').get(0)) {

					previewPar = parentSection.find('> p');
					previewParCurrentHeight = previewPar.css('height');
					previewPar.css('height', 'auto');
					previewParAnimateHeight = previewPar.css('height');
					previewPar.css('height', previewParCurrentHeight);

				}

				// Content
				toggleContent = parentSection.find('> .toggle-content');

				if (parentSection.hasClass('active')) {

					$(previewPar).animate({
						height: previewParAnimateHeight
					}, self.options.duration, function() {
						$(this).addClass('preview-active');
					});

					toggleContent.slideDown(self.options.duration, function() {
						if (closeElement) {
							closeElement.trigger('click');
						}
					});

				} else {

					$(previewPar).animate({
						height: 0
					}, self.options.duration, function() {
						$(this).removeClass('preview-active');
					});

					toggleContent.slideUp(self.options.duration);

				}

			});
		}
	};

	// expose to scope
	$.extend(theme, {
		PluginToggle: PluginToggle
	});

	// jquery plugin
	$.fn.themePluginToggle = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginToggle($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// Tweets
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__tweets';

	var PluginTweets = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginTweets.defaults = {
		username: null,
		count: 2,
		URL: 'php/twitter-feed.php',
		iconColor: false
	};

	PluginTweets.prototype = {
		initialize: function($el, opts) {
			if ($el.data(instanceName)) {
				return this;
			}

			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginTweets.defaults, opts, {
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			if (this.options.username == null || this.options.username == '') {
				return this;
			}

			var self = this,
				$wrapper = this.options.wrapper;

			$.ajax({
				type: 'GET',
				data: {
					twitter_screen_name: self.options.username,
					tweets_to_display: self.options.count,
					icon_color: self.options.iconColor
				},
				url: self.options.URL,
			}).done(function(html) {
				$wrapper.html(html).find('a').attr('target','_blank');
			});

			return this;
		}
	};

	// expose to scope
	$.extend(theme, {
		PluginTweets: PluginTweets
	});

	// jquery plugin
	$.fn.themePluginTweets = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginTweets($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// Validation
(function(theme, $) {

	theme = theme || {};

	$.extend(theme, {

		PluginValidation: {

			defaults: {
				formClass: 'needs-validation',
				validator: {
					highlight: function(element) {
						$(element)
							.addClass('is-invalid')
							.removeClass('is-valid')
							.parent()
							.removeClass('has-success')
							.addClass('has-danger');
					},
					success: function(label, element) {
						$(element)
							.removeClass('is-invalid')
							.addClass('is-valid')
							.parent()
							.removeClass('has-danger')
							.addClass('has-success')
							.find('label.error')
							.remove();
					},
					errorPlacement: function(error, element) {
						if (element.attr('type') == 'radio' || element.attr('type') == 'checkbox') {
							error.appendTo(element.parent().parent());
						} else {
							error.insertAfter(element);
						}
					}
				}
			},

			initialize: function(opts) {
				initialized = true;

				this
					.setOptions(opts)
					.build();

				return this;
			},

			setOptions: function(opts) {
				this.options = $.extend(true, {}, this.defaults, opts);

				return this;
			},

			build: function() {
				var self = this;

				if (!($.isFunction($.validator))) {
					return this;
				}

				self.setMessageGroups();

				$.validator.setDefaults(self.options.validator);

				$('.' + self.options.formClass).validate();

				return this;
			},

			setMessageGroups: function() {

				$('.checkbox-group[data-msg-required], .radio-group[data-msg-required]').each(function() {
					var message = $(this).data('msg-required');
					$(this).find('input').attr('data-msg-required', message);
				});

			}

		}

	});

}).apply(this, [window.theme, jQuery]);

// Video Background
(function(theme, $) {

	theme = theme || {};

	var instanceName = '__videobackground';

	var PluginVideoBackground = function($el, opts) {
		return this.initialize($el, opts);
	};

	PluginVideoBackground.defaults = {
		overlay: false,
		volume: 1,
		playbackRate: 1,
		muted: true,
		loop: true,
		autoplay: true,
		position: '50% 50%',
		posterType: 'detect',
		className: 'vide-video-wrapper'
	};

	PluginVideoBackground.prototype = {
		initialize: function($el, opts) {
			this.$el = $el;

			this
				.setData()
				.setOptions(opts)
				.build()
				.events();

			return this;
		},

		setData: function() {
			this.$el.data(instanceName, this);

			return this;
		},

		setOptions: function(opts) {
			this.options = $.extend(true, {}, PluginVideoBackground.defaults, opts, {
				path: this.$el.data('video-path'),
				wrapper: this.$el
			});

			return this;
		},

		build: function() {
			var self = this;

			if (!($.isFunction($.fn.vide)) || (!this.options.path)) {
				return this;
			}

			if (this.options.overlay) {

				var overlayClass = this.options.overlayClass;

				this.options.wrapper.prepend(
					$('<div />').addClass(overlayClass)
				);
			}

			this.options.wrapper
				.vide(this.options.path, this.options)
				.first()
				.css('z-index', 0);

			// Change Poster
			self.changePoster();

			// Initialize Vide inside a carousel
			if( self.options.wrapper.closest('.owl-carousel').get(0) ) {
				self.options.wrapper.closest('.owl-carousel').on('initialized.owl.carousel', function(){
					$('.owl-item.cloned')
						.find('[data-plugin-video-background] .vide-video-wrapper')
						.remove();

					$('.owl-item.cloned')
						.find('[data-plugin-video-background]')
						.vide(self.options.path, self.options)
						.first()
						.css('z-index', 0);

					self.changePoster( self.options.wrapper.closest('.owl-carousel') );
				});
			}

			// Play Video Button
			var $playButton = self.options.wrapper.find('.video-background-play');

			if( $playButton.get(0) ) {
				var $playWrapper = self.options.wrapper.find('.video-background-play-wrapper');

				self.options.wrapper.find('.video-background-play').on('click', function(e){
					e.preventDefault();

					if( $playWrapper.get(0) ) {
						$playWrapper.animate({
							opacity: 0
						}, 300, function(){
							$playWrapper.parent().height( $playWrapper.outerHeight() );
							$playWrapper.remove();
						});
					} else {
						$playButton.animate({
							opacity: 0
						}, 300, function(){
							$playButton.remove();
						});
					}

					setTimeout(function(){
						self.options.wrapper.find('video')[0].play();
					}, 500)
				});
			}

			$(window).trigger('vide.video.inserted.on.dom');

			return this;
		},

		changePoster: function( $carousel ) {
			var self = this;

			// If it's inside carousel
			if( $carousel && self.options.changePoster ) {
				$carousel.find('.owl-item [data-plugin-video-background] .vide-video-wrapper').css({
					'background-image': 'url(' + self.options.changePoster + ')'
				});

				return this;
			}

			if( self.options.changePoster ) {
				self.options.wrapper.find('.vide-video-wrapper').css({
					'background-image': 'url(' + self.options.changePoster + ')'
				});
			}

			return this;
		},

		events: function() {
			var self = this;

			// Initialize
			self.options.wrapper.on('video.background.initialize', function(){
				self.build();
			});

			return this;
		}
	};

	// expose to scope
	$.extend(theme, {
		PluginVideoBackground: PluginVideoBackground
	});

	// jquery plugin
	$.fn.themePluginVideoBackground = function(opts) {
		return this.map(function() {
			var $this = $(this);

			if ($this.data(instanceName)) {
				return $this.data(instanceName);
			} else {
				return new PluginVideoBackground($this, opts);
			}

		});
	}

}).apply(this, [window.theme, jQuery]);

// Account
(function(theme, $) {

	theme = theme || {};

	var initialized = false;

	$.extend(theme, {

		Account: {

			defaults: {
				wrapper: $('#headerAccount')
			},

			initialize: function($wrapper, opts) {
				if (initialized) {
					return this;
				}

				initialized = true;
				this.$wrapper = ($wrapper || this.defaults.wrapper);

				this
					.setOptions(opts)
					.events();

				return this;
			},

			setOptions: function(opts) {
				this.options = $.extend(true, {}, this.defaults, opts, theme.fn.getOptions(this.$wrapper.data('plugin-options')));

				return this;
			},

			events: function() {
				var self = this;

				$(window).on('load', function(){
					$(document).ready(function(){
						setTimeout(function(){

							self.$wrapper.find('input').on('focus', function() {
								self.$wrapper.addClass('open');

								$(document).mouseup(function(e) {
									if (!self.$wrapper.is(e.target) && self.$wrapper.has(e.target).length === 0) {
										self.$wrapper.removeClass('open');
									}
								});
							});

						}, 1500);
					});
				});

				$('#headerSignUp').on('click', function(e) {
					e.preventDefault();
					self.$wrapper.addClass('signup').removeClass('signin').removeClass('recover');
					self.$wrapper.find('.signup-form input:first').focus();
				});

				$('#headerSignIn').on('click', function(e) {
					e.preventDefault();
					self.$wrapper.addClass('signin').removeClass('signup').removeClass('recover');
					self.$wrapper.find('.signin-form input:first').focus();
				});

				$('#headerRecover').on('click', function(e) {
					e.preventDefault();
					self.$wrapper.addClass('recover').removeClass('signup').removeClass('signin');
					self.$wrapper.find('.recover-form input:first').focus();
				});

				$('#headerRecoverCancel').on('click', function(e) {
					e.preventDefault();
					self.$wrapper.addClass('signin').removeClass('signup').removeClass('recover');
					self.$wrapper.find('.signin-form input:first').focus();
				});
			}

		}

	});

}).apply(this, [window.theme, jQuery]);

// Nav
(function(theme, $) {

	theme = theme || {};

	var initialized = false;

	$.extend(theme, {

		Nav: {

			defaults: {
				wrapper: $('#mainNav'),
				scrollDelay: 600,
				scrollAnimation: 'easeOutQuad'
			},

			initialize: function($wrapper, opts) {
				if (initialized) {
					return this;
				}

				initialized = true;
				this.$wrapper = ($wrapper || this.defaults.wrapper);

				this
					.setOptions(opts)
					.build()
					.events();

				return this;
			},

			setOptions: function(opts) {
				this.options = $.extend(true, {}, this.defaults, opts, theme.fn.getOptions(this.$wrapper.data('plugin-options')));

				return this;
			},

			build: function() {
				var self = this,
					$html = $('html'),
					$header = $('#header'),
					$headerNavMain = $('#header .header-nav-main'),
					thumbInfoPreview;

				// Preview Thumbs
				if( self.$wrapper.find('a[data-thumb-preview]').length ) {
					self.$wrapper.find('a[data-thumb-preview]').each(function() {
						thumbInfoPreview = $('<span />').addClass('thumb-info thumb-info-preview')
												.append($('<span />').addClass('thumb-info-wrapper')
													.append($('<span />').addClass('thumb-info-image').css('background-image', 'url(' + $(this).data('thumb-preview') + ')')
											   )
										   );

						$(this).append(thumbInfoPreview);
					});
				}

				// Side Header / Side Header Hamburguer Sidebar (Reverse Dropdown)
				if($html.hasClass('side-header') || $html.hasClass('side-header-hamburguer-sidebar')) {
					
					// Side Header Right / Side Header Hamburguer Sidebar Right
					if($html.hasClass('side-header-right') || $html.hasClass('side-header-hamburguer-sidebar-right')) {
						if(!$html.hasClass('side-header-right-no-reverse')) {
							$header.find('.dropdown-submenu').addClass('dropdown-reverse');
						}
					}

				} else {
					
					// Reverse
					var checkReverseFlag = false;
					self.checkReverse = function() {
						if( !checkReverseFlag ) {
							self.$wrapper.find('.dropdown, .dropdown-submenu').removeClass('dropdown-reverse');

							self.$wrapper.find('.dropdown:not(.manual):not(.dropdown-mega), .dropdown-submenu:not(.manual)').each(function() {
								if(!$(this).find('.dropdown-menu').visible( false, true, 'horizontal' )  ) {
									$(this).addClass('dropdown-reverse');
								}
							});

							checkReverseFlag = true;
						}
					}

					$(window).on('resize', function(){
						checkReverseFlag = false;
					});

					$header.on('mouseover', function(){
						self.checkReverse();
					});

				}

				// Clone Items
				if($headerNavMain.hasClass('header-nav-main-clone-items')) {

			    	$headerNavMain.find('nav > ul > li > a').each(function(){
				    	var parent = $(this).parent(),
				    		clone  = $(this).clone(),
				    		clone2 = $(this).clone(),
				    		wrapper = $('<span class="wrapper-items-cloned"></span>');

				    	// Config Classes
				    	$(this).addClass('item-original');
				    	clone2.addClass('item-two');

				    	// Insert on DOM
				    	parent.prepend(wrapper);
				    	wrapper.append(clone).append(clone2);
				    });

				}

				// Floating
				if($('#header.header-floating-icons').length && $(window).width() > 991) {

					var menuFloatingAnim = {
						$menuFloating: $('#header.header-floating-icons .header-container > .header-row'),

						build: function() {
							var self = this;

							self.init();
						},
						init: function(){
							var self  = this,
								divisor = 0;

							$(window).scroll(function() {
							    var scrollPercent = 100 * $(window).scrollTop() / ($(document).height() - $(window).height()),
							    	st = $(this).scrollTop();

								divisor = $(document).height() / $(window).height();

							    self.$menuFloating.find('.header-column > .header-row').css({
							    	transform : 'translateY( calc('+ scrollPercent +'vh - '+ st / divisor +'px) )' 
							    });
							});
						}
					}

					menuFloatingAnim.build();

				}

				// Slide
				if($('.header-nav-links-vertical-slide').length) {
					var slideNavigation = {
						$mainNav: $('#mainNav'),
						$mainNavItem: $('#mainNav li'),

						build: function(){
							var self = this;

							self.menuNav();
						},
						menuNav: function(){
							var self = this;

							self.$mainNavItem.on('click', function(e){
								var currentMenuItem 	= $(this),
									currentMenu 		= $(this).parent(),
									nextMenu        	= $(this).find('ul').first(),
									prevMenu        	= $(this).closest('.next-menu'),
									isSubMenu       	= currentMenuItem.hasClass('dropdown') || currentMenuItem.hasClass('dropdown-submenu'),
									isBack          	= currentMenuItem.hasClass('back-button'),
									nextMenuHeightDiff  = ( ( nextMenu.find('> li').length * nextMenu.find('> li').outerHeight() ) - nextMenu.outerHeight() ),
									prevMenuHeightDiff  = ( ( prevMenu.find('> li').length * prevMenu.find('> li').outerHeight() ) - prevMenu.outerHeight() );

								if( isSubMenu ) {
									currentMenu.addClass('next-menu');
									nextMenu.addClass('visible');
									currentMenu.css({
										overflow: 'visible',
										'overflow-y': 'visible'
									});
									
									if( nextMenuHeightDiff > 0 ) {
										nextMenu.css({
											overflow: 'hidden',
											'overflow-y': 'scroll'
										});
									}

									for( i = 0; i < nextMenu.find('> li').length; i++ ) {
										if( nextMenu.outerHeight() < ($('.header-row-side-header').outerHeight() - 100) ) {
											nextMenu.css({
												height: nextMenu.outerHeight() + nextMenu.find('> li').outerHeight()
											});
										}
									}

									nextMenu.css({
										'padding-top': nextMenuHeightDiff + 'px'
									});
								}

								if( isBack ) {
									currentMenu.parent().parent().removeClass('next-menu');
									currentMenu.removeClass('visible');

									if( prevMenuHeightDiff > 0 ) {
										prevMenu.css({
											overflow: 'hidden',
											'overflow-y': 'scroll'
										});
									}
								}

								e.stopPropagation();
							});
						}
					}

					$(window).trigger('resize');
					
					if( $(window).width() > 991 ) {
						slideNavigation.build();
					}

					$(document).ready(function(){
						$(window).afterResize(function(){
							if( $(window).width() > 991 ) {
								slideNavigation.build();
							}
						});
					});
				}

				// Header Nav Main Mobile Dark
				if($('.header-nav-main-mobile-dark').length) {
					$('#header:not(.header-transparent-dark-bottom-border):not(.header-transparent-light-bottom-border)').addClass('header-no-border-bottom');
				}

				// Keyboard Navigation / Accessibility
				if( $(window).width() > 991 ) {
					var focusFlag = false;
					$header.find('.header-nav-main nav > ul > li > a').on('focus', function(){
						
						if( $(window).width() > 991 ) {
							if( !focusFlag ) {
								focusFlag = true;
								$(this).trigger('blur');
								
								self.focusMenuWithChildren();
							}
						}

					});
				}
				
				return this;
			},

			focusMenuWithChildren: function() {
				// Get all the link elements within the primary menu.
				var links, i, len,
					menu = document.querySelector( 'html:not(.side-header):not(.side-header-hamburguer-sidebar):not(.side-header-overlay-full-screen) .header-nav-main > nav' );

				if ( ! menu ) {
					return false;
				}

				links = menu.getElementsByTagName( 'a' );

				// Each time a menu link is focused or blurred, toggle focus.
				for ( i = 0, len = links.length; i < len; i++ ) {
					links[i].addEventListener( 'focus', toggleFocus, true );
					links[i].addEventListener( 'blur', toggleFocus, true );
				}

				//Sets or removes the .focus class on an element.
				function toggleFocus() {
					var self = this;

					// Move up through the ancestors of the current link until we hit .primary-menu.
					while ( -1 === self.className.indexOf( 'header-nav-main' ) ) {
						// On li elements toggle the class .focus.
						if ( 'li' === self.tagName.toLowerCase() ) {
							if ( -1 !== self.className.indexOf( 'accessibility-open' ) ) {
								self.className = self.className.replace( ' accessibility-open', '' );
							} else {
								self.className += ' accessibility-open';
							}
						}
						self = self.parentElement;
					}
				}
			},

			events: function() {
				var self    = this,
					$html   = $('html'),
					$header = $('#header'),
					$window = $(window),
					headerBodyHeight = $('.header-body').outerHeight();

				if( $header.hasClass('header') ) {
					$header = $('.header');
				}

				$header.find('a[href="#"]').on('click', function(e) {
					e.preventDefault();
				});

				// Mobile Arrows
				if( $html.hasClass('side-header-hamburguer-sidebar') ) {
					$header.find('.dropdown-toggle, .dropdown-submenu > a')
						.append('<i class="fas fa-chevron-down fa-chevron-right"></i>');
				} else {
					$header.find('.dropdown-toggle, .dropdown-submenu > a')
						.append('<i class="fas fa-chevron-down"></i>');
				}
				
				$header.find('.dropdown-toggle[href="#"], .dropdown-submenu a[href="#"], .dropdown-toggle[href!="#"] .fa-chevron-down, .dropdown-submenu a[href!="#"] .fa-chevron-down').on('click', function(e) {
					e.preventDefault();
					if ($window.width() < 992) {
						$(this).closest('li').toggleClass('open');

						// Adjust Header Body Height
						var height = ( $header.hasClass('header-effect-shrink') && $html.hasClass('sticky-header-active') ) ? theme.StickyHeader.options.stickyHeaderContainerHeight : headerBodyHeight;
						$('.header-body').animate({
					 		height: ($('.header-nav-main nav').outerHeight(true) + height) + 10
					 	}, 0);
					}
				});

				$header.find('li a.active').addClass('current-page-active');

				// Add Open Class
				$header.find('.header-nav-click-to-open .dropdown-toggle[href="#"], .header-nav-click-to-open .dropdown-submenu a[href="#"], .header-nav-click-to-open .dropdown-toggle > i').on('click', function(e) {
					if( !$('html').hasClass('side-header-hamburguer-sidebar') && $window.width() > 991 ) {
						e.preventDefault();
						e.stopPropagation();
					}

					if ($window.width() > 991) {
						e.preventDefault();
						e.stopPropagation();

						$header.find('li a.active').removeClass('active');

						if( $(this).prop('tagName') == 'I' ) {
							$(this).parent().addClass('active');
						} else {
							$(this).addClass('active');
						}

						if (!$(this).closest('li').hasClass('open')) {

							var $li = $(this).closest('li'),
								isSub = false;

							if( $(this).prop('tagName') == 'I' ) {
								$('#header .dropdown.open').removeClass('open');
								$('#header .dropdown-menu .dropdown-submenu.open').removeClass('open');
							}

							if ( $(this).parent().hasClass('dropdown-submenu') ) {
								isSub = true;
							}

							$(this).closest('.dropdown-menu').find('.dropdown-submenu.open').removeClass('open');
							$(this).parent('.dropdown').parent().find('.dropdown.open').removeClass('open');

							if (!isSub) {
								$(this).parent().find('.dropdown-submenu.open').removeClass('open');
							}

							$li.addClass('open');

							$(document).off('click.nav-click-to-open').on('click.nav-click-to-open', function (e) {
								if (!$li.is(e.target) && $li.has(e.target).length === 0) {
									$li.removeClass('open');
									$li.parents('.open').removeClass('open');
									$header.find('li a.active').removeClass('active');
									$header.find('li a.current-page-active').addClass('active');
								}
							});

						} else {
							$(this).closest('li').removeClass('open');
							$header.find('li a.active').removeClass('active');
							$header.find('li a.current-page-active').addClass('active');
						}

						$window.trigger({
							type: 'resize',
							from: 'header-nav-click-to-open'
						});
					}
				});

				// Collapse Nav
				$header.find('[data-collapse-nav]').on('click', function(e) {
					$(this).parents('.collapse').removeClass('show');
				});

				// Top Features
				$header.find('.header-nav-features-toggle').on('click', function(e) {
					e.preventDefault();

					var $toggleParent = $(this).parent();

					if (!$(this).siblings('.header-nav-features-dropdown').hasClass('show')) {

						var $dropdown = $(this).siblings('.header-nav-features-dropdown');

						$('.header-nav-features-dropdown.show').removeClass('show');

						$dropdown.addClass('show');

						$(document).off('click.header-nav-features-toggle').on('click.header-nav-features-toggle', function (e) {
							if (!$toggleParent.is(e.target) && $toggleParent.has(e.target).length === 0) {
								$('.header-nav-features-dropdown.show').removeClass('show');
							}
						});

						if ($(this).attr('data-focus')) {
							$('#' + $(this).attr('data-focus')).focus();
						}

					} else {
						$(this).siblings('.header-nav-features-dropdown').removeClass('show');
					}
				});

				// Hamburguer Menu
				var $hamburguerMenuBtn = $('.hamburguer-btn:not(.side-panel-toggle)'),
					$hamburguerSideHeader = $('#header.side-header, #header.side-header-overlay-full-screen');
				
				$hamburguerMenuBtn.on('click', function(){
					if($(this).attr('data-set-active') != 'false') {
						$(this).toggleClass('active');
					}
					$hamburguerSideHeader.toggleClass('side-header-hide');
					$html.toggleClass('side-header-hide');

					$window.trigger('resize');
				});

				$('.hamburguer-close:not(.side-panel-toggle)').on('click', function(){
					$('.hamburguer-btn:not(.hamburguer-btn-side-header-mobile-show)').trigger('click');
				});				
				
				// Set Header Body Height when open mobile menu
				$('.header-nav-main nav').on('show.bs.collapse', function () {
				 	$(this).removeClass('closed');

				 	// Add Mobile Menu Opened Class
				 	$('html').addClass('mobile-menu-opened');

			 		$('.header-body').animate({
				 		height: ($('.header-body').outerHeight() + $('.header-nav-main nav').outerHeight(true)) + 10
				 	});

				 	// Header Below Slider / Header Bottom Slider - Scroll to menu position
				 	if( $('#header').is('.header-bottom-slider, .header-below-slider') && !$('html').hasClass('sticky-header-active') ) {
				 		self.scrollToTarget( $('#header'), 0 );
				 	}
				});

				// Set Header Body Height when collapse mobile menu
				$('.header-nav-main nav').on('hide.bs.collapse', function () {
				 	$(this).addClass('closed');

				 	// Remove Mobile Menu Opened Class
				 	$('html').removeClass('mobile-menu-opened');

			 		$('.header-body').animate({
				 		height: ($('.header-body').outerHeight() - $('.header-nav-main nav').outerHeight(true))
				 	}, function(){
				 		$(this).height('auto');
				 	});
				});

				// Header Effect Shrink - Adjust header body height on mobile
				$window.on('stickyHeader.activate', function(){
					if( $window.width() < 992 && $header.hasClass('header-effect-shrink') ) {
						if( $('.header-btn-collapse-nav').attr('aria-expanded') == 'true' ) {
							$('.header-body').animate({
						 		height: ( $('.header-nav-main nav').outerHeight(true) + theme.StickyHeader.options.stickyHeaderContainerHeight ) + ( ($('.header-nav-bar').length) ? $('.header-nav-bar').outerHeight() : 0 ) 
						 	});
						}
					}
				});

				$window.on('stickyHeader.deactivate', function(){
					if( $window.width() < 992 && $header.hasClass('header-effect-shrink') ) {
						if( $('.header-btn-collapse-nav').attr('aria-expanded') == 'true' ) {
							$('.header-body').animate({
						 		height: headerBodyHeight + $('.header-nav-main nav').outerHeight(true) + 10
						 	});
						}
					}
				});

				// Remove Open Class on Resize		
				$window.on('resize.removeOpen', function(e) {
					if( e.from == 'header-nav-click-to-open' ) {
						return;
					}
					
					setTimeout(function() {
						if( $window.width() > 991 ) {
							$header.find('.dropdown.open').removeClass('open');
						}
					}, 100);
				});

				// Side Header - Change value of initial header body height
				$(document).ready(function(){
					if( $window.width() > 991 ) {
						var flag = false;
						
						$window.on('resize', function(e) {
							if( e.from == 'header-nav-click-to-open' ) {
								return;
							}

							$header.find('.dropdown.open').removeClass('open');

							if( $window.width() < 992 && flag == false ) {
								headerBodyHeight = $('.header-body').outerHeight();
								flag = true;

								setTimeout(function(){
									flag = false;
								}, 500);
							}
						});
					}
				});

				// Side Header - Set header height on mobile
				if( $html.hasClass('side-header') ) {
					if( $window.width() < 992 ) {
						$header.css({
							height: $('.header-body .header-container').outerHeight() + (parseInt( $('.header-body').css('border-top-width') ) + parseInt( $('.header-body').css('border-bottom-width') ))
						});
					}

					$(document).ready(function(){
						$window.afterResize(function(){
							if( $window.width() < 992 ) {
								$header.css({
									height: $('.header-body .header-container').outerHeight() + (parseInt( $('.header-body').css('border-top-width') ) + parseInt( $('.header-body').css('border-bottom-width') ))
								});
							} else {
								$header.css({
									height: ''
								});
							}
						});
					});
				}

				// Anchors Position
				if( $('[data-hash]').length ) {
					$('[data-hash]').on('mouseover', function(){
						var $this = $(this);

						if( !$this.data('__dataHashBinded') ) {
							var target = $this.attr('href'),
								offset = ($this.is("[data-hash-offset]") ? $this.data('hash-offset') : 0),
								delay  = ($this.is("[data-hash-delay]") ? $this.data('hash-delay') : 0),
								force  = ($this.is("[data-hash-force]") ? true : false),
								windowWidth = $(window).width();

							// Hash Offset SM
							if ($this.is("[data-hash-offset-sm]") && windowWidth > 576) {
								offset = $this.data('hash-offset-sm');
							}
							
							// Hash Offset MD
							if ($this.is("[data-hash-offset-md]") && windowWidth > 768) {
								offset = $this.data('hash-offset-md');
							}
							
							// Hash Offset LG
							if ($this.is("[data-hash-offset-lg]") && windowWidth > 992) {
								offset = $this.data('hash-offset-lg');
							}
							
							// Hash Offset XL
							if ($this.is("[data-hash-offset-xl]") && windowWidth > 1200) {
								offset = $this.data('hash-offset-xl');
							}
							
							// Hash Offset XXL
							if ($this.is("[data-hash-offset-xxl]") && windowWidth > 1400) {
								offset = $this.data('hash-offset-xxl');
							}

							if( !$(target).length ) {
								target = target.split('#');
								target = '#'+target[1];
							}

							if( target.indexOf('#') != -1 && $(target).length) {
								$this.on('click', function(e) {
									e.preventDefault();

									if( !$(e.target).is('i') || force ) {

										setTimeout(function(){

											// Close Collapse if open
											$this.parents('.collapse.show').collapse('hide');

											// Close Side Header
											$hamburguerSideHeader.addClass('side-header-hide');
											$html.addClass('side-header-hide');
											
											$window.trigger('resize');

											self.scrollToTarget(target, offset);

											// Data Hash Trigger Click
											if( $this.data('hash-trigger-click') ) {

												var $clickTarget = $( $this.data('hash-trigger-click') ),
													clickDelay = $this.data('hash-trigger-click-delay') ? $this.data('hash-trigger-click-delay') : 0;

												if( $clickTarget.length ) {

													setTimeout(function(){
														// If is a "Tabs" plugin link
														if( $clickTarget.closest('.nav-tabs').length ) {
															new bootstrap.Tab( $clickTarget[0] ).show();
														} else {
															$clickTarget.trigger('click');
														}
														
													}, clickDelay);
												}

											}

										}, delay);
										
									}

									return;
								});
							}

							$(this).data('__dataHashBinded', true);
						}
					});
				}

				// Floating
				if($('#header.header-floating-icons').length) {

					$('#header.header-floating-icons [data-hash]').off().each(function() {

						var target = $(this).attr('href'),
							offset = ($(this).is("[data-hash-offset]") ? $(this).data('hash-offset') : 0);

						if($(target).length) {
							$(this).on('click', function(e) {
								e.preventDefault();

									$('html, body').animate({
										scrollTop: $(target).offset().top - offset
									}, 600, 'easeOutQuad', function() {

									});

								return;
							});
						}

					});

				}

				// Side Panel Toggle
				if( $('.side-panel-toggle').length ) {
					var init_html_class = $('html').attr('class');

					$('.side-panel-toggle').on('click', function(e){
						var extra_class = $(this).data('extra-class'),
							delay       = ( extra_class ) ? 100 : 0,
							isActive    = $(this).data('is-active') ? $(this).data('is-active') : false;

						e.preventDefault();

						if( isActive ) {
							$('html').removeClass('side-panel-open');
							$(this).data('is-active', false);
							return false;
						}

						if( extra_class ) {
							$('.side-panel-wrapper').css('transition','none');
							$('html')
								.removeClass()
								.addClass( init_html_class )
								.addClass( extra_class );
						}
						setTimeout(function(){
							$('.side-panel-wrapper').css('transition','');
							$('html').toggleClass('side-panel-open');
						}, delay);

						$(this).data('is-active', true);						
					});

					$(document).on('click', function(e){
						if( !$(e.target).closest('.side-panel-wrapper').length && !$(e.target).hasClass('side-panel-toggle') ) {
							$('.hamburguer-btn.side-panel-toggle:not(.side-panel-close)').removeClass('active');
							$('html').removeClass('side-panel-open');
							$('.side-panel-toggle').data('is-active', false);
						}
					});
				}

				return this;
			},

			scrollToTarget: function(target, offset) {
				var self = this,
					targetPosition = $(target).offset().top;

				$('body').addClass('scrolling');

				$('html, body').animate({
					scrollTop: $(target).offset().top - offset
				}, self.options.scrollDelay, self.options.scrollAnimation, function() {
					$('body').removeClass('scrolling');

					// If by some reason the scroll finishes in a wrong position, this code will run the scrollToTarget() again until get the correct position
					// We need do it just one time to prevent infinite recursive loop at scrollToTarget() function
					if( $(target).offset().top !=  targetPosition) {
						$('html, body').animate({
							scrollTop: $(target).offset().top - offset
						}, 1, self.options.scrollAnimation, function() {});
					}
				});

				return this;

			}

		}

	});

}).apply(this, [window.theme, jQuery]);


// Newsletter
(function(theme, $) {

	theme = theme || {};

	var initialized = false;

	$.extend(theme, {

		Newsletter: {

			defaults: {
				wrapper: $('#newsletterForm')
			},

			initialize: function($wrapper, opts) {
				if (initialized) {
					return this;
				}

				initialized = true;
				this.$wrapper = ($wrapper || this.defaults.wrapper);

				this
					.setOptions(opts)
					.build();

				return this;
			},

			setOptions: function(opts) {
				this.options = $.extend(true, {}, this.defaults, opts, theme.fn.getOptions(this.$wrapper.data('plugin-options')));

				return this;
			},

			build: function() {
				if (!($.isFunction($.fn.validate))) {
					return this;
				}

				var self = this,
					$email = self.$wrapper.find('#newsletterEmail'),
					$success = $('#newsletterSuccess'),
					$error = $('#newsletterError');

				self.$wrapper.validate({
					submitHandler: function(form) {

						$.ajax({
							type: 'POST',
							url: self.$wrapper.attr('action'),
							data: {
								'email': $email.val()
							},
							dataType: 'json',
							success: function(data) {
								if (data.response == 'success') {

									$success.removeClass('d-none');
									$error.addClass('d-none');

									$email
										.val('')
										.blur()
										.closest('.control-group')
										.removeClass('success')
										.removeClass('error');

								} else {

									$error.html(data.message);
									$error.removeClass('d-none');
									$success.addClass('d-none');

									$email
										.blur()
										.closest('.control-group')
										.removeClass('success')
										.addClass('error');

								}
							}
						});

					},
					rules: {
						newsletterEmail: {
							required: true,
							email: true
						}
					},
					errorPlacement: function(error, element) {

					}
				});

				return this;
			}

		}

	});

}).apply(this, [window.theme, jQuery]);

// Search
(function(theme, $) {

	theme = theme || {};

	var initialized = false;

	$.extend(theme, {

		Search: {

			defaults: {
				wrapper: $('#searchForm')
			},

			initialize: function($wrapper, opts) {
				if (initialized) {
					return this;
				}

				initialized = true;
				this.$wrapper = ($wrapper || this.defaults.wrapper);

				this
					.setOptions(opts)
					.build();

				return this;
			},

			setOptions: function(opts) {
				this.options = $.extend(true, {}, this.defaults, opts, theme.fn.getOptions(this.$wrapper.data('plugin-options')));

				return this;
			},

			build: function() {
				if (!($.isFunction($.fn.validate))) {
					return this;
				}

				this.$wrapper.validate({
					errorPlacement: function(error, element) {}
				});

				// Search Reveal
				theme.fn.execOnceTroughEvent( '#header', 'mouseover.search.reveal', function(){
					$('.header-nav-features-search-reveal').each(function() {
						var $el = $(this),
							$header = $('#header'),
							$html = $('html');

						$el.find('.header-nav-features-search-show-icon').on('click', function() {
							$el.addClass('show');
							$header.addClass('search-show');
							$html.addClass('search-show');
							$('#headerSearch').focus();
						});

						$el.find('.header-nav-features-search-hide-icon').on('click', function() {
							$el.removeClass('show');
							$header.removeClass('search-show');
							$html.removeClass('search-show');
						});
					});
				} );

				return this;
			}

		}

	});

}).apply(this, [window.theme, jQuery]);

// Sticky Header
(function(theme, $) {

	theme = theme || {};

	var initialized = false;

	$.extend(theme, {

		StickyHeader: {

			defaults: {
				wrapper: $('#header'),
				headerBody: $('#header .header-body'),
				stickyEnabled: true,
				stickyEnableOnBoxed: true,
				stickyEnableOnMobile: false,
				stickyStartAt: 0,
				stickyStartAtElement: false,
				stickySetTop: 0,
				stickyEffect: '',
				stickyHeaderContainerHeight: false,
				stickyChangeLogo: false,
				stickyChangeLogoWrapper: true,
				stickyForce: false,
				stickyScrollUp: false,
				stickyScrollValue: 0
			},

			initialize: function($wrapper, opts) {
				if (initialized) {
					return this;
				}				

				initialized = true;
				this.$wrapper = ($wrapper || this.defaults.wrapper);

				if( this.$wrapper.hasClass('header') ) {
					this.$wrapper = $('.header[data-plugin-options]');
				}

				this
					.setOptions(opts)
					.build()
					.events();

				return this;
			},

			setOptions: function(opts) {
				this.options = $.extend(true, {}, this.defaults, opts, theme.fn.getOptions(this.$wrapper.data('plugin-options')));
				return this;
			},

			build: function() {
				if( $(window).width() < 992 && this.options.stickyEnableOnMobile == false ) {
					$('html').addClass('sticky-header-mobile-disabled');
					return this;
				}

				if (!this.options.stickyEnableOnBoxed && $('html').hasClass('boxed') || $('html').hasClass('side-header-hamburguer-sidebar') && !this.options.stickyForce || !this.options.stickyEnabled) {
					return this;
				}

				var self = this;

				if( self.options.wrapper.hasClass('header') ) {
					self.options.wrapper = $('.header');
					self.options.headerBody = $('.header .header-body');
				}
				
				var	$html = $('html'),
					$window = $(window),
					sideHeader = $html.hasClass('side-header'),
					initialHeaderTopHeight = self.options.wrapper.find('.header-top').outerHeight(),
					initialHeaderContainerHeight = self.options.wrapper.find('.header-container').outerHeight(),
					minHeight;

				// HTML Classes
				$html.addClass('sticky-header-enabled');

				if (parseInt(self.options.stickySetTop) < 0) {
					$html.addClass('sticky-header-negative');
				}

				if (self.options.stickyScrollUp) {
					$html.addClass('sticky-header-scroll-direction');
				}				

				// Notice Top Bar First Load
				if( $('.notice-top-bar').get(0) ) {
					if (parseInt(self.options.stickySetTop) == 1 || self.options.stickyEffect == 'shrink') {
						$('.body').on('transitionend webkitTransitionEnd oTransitionEnd', function () {
						    setTimeout(function(){
								if( !$html.hasClass('sticky-header-active') ) {
								    self.options.headerBody.animate({
								    	top: $('.notice-top-bar').outerHeight()
								    }, 300, function(){
								    	if( $html.hasClass('sticky-header-active') ) {
								    		self.options.headerBody.css('top', 0);
								    	}
								    });
								}
						    }, 0);
						});
					}					
				}

				// Set Start At
				if(self.options.stickyStartAtElement) {

					var $stickyStartAtElement = $(self.options.stickyStartAtElement);

					$(window).on('scroll resize sticky.header.resize', function() {
						self.options.stickyStartAt = $stickyStartAtElement.offset().top;
					});

					$(window).trigger('sticky.header.resize');
				}

				// Define Min Height value
				if( self.options.wrapper.find('.header-top').get(0) ) {
					minHeight = ( initialHeaderTopHeight + initialHeaderContainerHeight );
				} else {
					minHeight = initialHeaderContainerHeight;
				}

				// Set Wrapper Min-Height
				if( !sideHeader ) {
					if( !$('.header-logo-sticky-change').get(0) ) {
						self.options.wrapper.css('height', self.options.headerBody.outerHeight());
					} else {
						$window.on('stickyChangeLogo.loaded', function(){
							self.options.wrapper.css('height', self.options.headerBody.outerHeight());
						});
					}

					if( self.options.stickyEffect == 'shrink' ) {
						
						// Prevent wrong visualization of header when reload on middle of page
						$(document).ready(function(){
							if( $window.scrollTop() >= self.options.stickyStartAt ) {
								self.options.wrapper.find('.header-container').on('transitionend webkitTransitionEnd oTransitionEnd', function(){
									self.options.headerBody.css('position', 'fixed');
								});
							} else {
								if( !$html.hasClass('boxed') ) {
									self.options.headerBody.css('position', 'fixed');
								}
							}
						});

						self.options.wrapper.find('.header-container').css('height', initialHeaderContainerHeight);
						self.options.wrapper.find('.header-top').css('height', initialHeaderTopHeight);
					}
				}

				// Sticky Header Container Height
				if( self.options.stickyHeaderContainerHeight ) {
					self.options.wrapper.find('.header-container').css('height', self.options.wrapper.find('.header-container').outerHeight());
				}

				// Boxed
				if($html.hasClass('boxed') && self.options.stickyEffect == 'shrink') {
					self.boxedLayout();
				}

				// Check Sticky Header / Flags prevent multiple runs at same time
				var activate_flag   	 = true,
					deactivate_flag 	 = false,
					initialStickyStartAt = self.options.stickyStartAt;

				self.checkStickyHeader = function() {

					// Notice Top Bar
					var $noticeTopBar = $('.notice-top-bar');
					if ( $noticeTopBar.get(0) ) {
						self.options.stickyStartAt = ( $noticeTopBar.data('sticky-start-at') ) ? $noticeTopBar.data('sticky-start-at') : $('.notice-top-bar').outerHeight();
					} else {
						if( $html.hasClass('boxed') ) {
							self.options.stickyStartAt = initialStickyStartAt + 25;
						} else {
							self.options.stickyStartAt = initialStickyStartAt;
						}
					}

					if( $window.width() > 991 && $html.hasClass('side-header') ) {
						$html.removeClass('sticky-header-active');
						activate_flag = true;
						return;
					}

					if ($window.scrollTop() >= parseInt(self.options.stickyStartAt)) {
						if( activate_flag ) {
							self.activateStickyHeader();
							activate_flag = false;
							deactivate_flag = true;
						}
					} else {
						if( deactivate_flag ) {
							self.deactivateStickyHeader();
							deactivate_flag = false;
							activate_flag = true;
						}
					}

					// Scroll Up
					if (self.options.stickyScrollUp) {
						
					    // Get the new Value
					    self.options.stickyScrollNewValue = window.pageYOffset;

					    //Subtract the two and conclude
					    if(self.options.stickyScrollValue - self.options.stickyScrollNewValue < 0){
					        $html.removeClass('sticky-header-scroll-up').addClass('sticky-header-scroll-down');
					    } else if(self.options.stickyScrollValue - self.options.stickyScrollNewValue > 0){
					        $html.removeClass('sticky-header-scroll-down').addClass('sticky-header-scroll-up');
					    }

					    // Update the old value
					    self.options.stickyScrollValue = self.options.stickyScrollNewValue;

					}
				};
				
				// Activate Sticky Header
				self.activateStickyHeader = function() {
					if ($window.width() < 992) {
						if (self.options.stickyEnableOnMobile == false) {
							self.deactivateStickyHeader();
							self.options.headerBody.css({
								position: 'relative'
							});
							return false;
						}
					} else {
						if (sideHeader) {
							self.deactivateStickyHeader();
							return;
						}
					}

					$html.addClass('sticky-header-active');

					// Sticky Effect - Reveal
					if( self.options.stickyEffect == 'reveal' ) {

						self.options.headerBody.css('top','-' + self.options.stickyStartAt + 'px');

						self.options.headerBody.animate({
							top: self.options.stickySetTop
						}, 400, function() {});

					}

					// Sticky Effect - Shrink
					if( self.options.stickyEffect == 'shrink' ) {

						// If Header Top
						if( self.options.wrapper.find('.header-top').get(0) ) {
							self.options.wrapper.find('.header-top').css({
								height: 0,
								'min-height': 0,
								overflow: 'hidden'
							});
						}

						// Header Container
						if( self.options.stickyHeaderContainerHeight ) {
							self.options.wrapper.find('.header-container').css({
								height: self.options.stickyHeaderContainerHeight,
								'min-height': 0
							});
						} else {
							self.options.wrapper.find('.header-container').css({
								height: (initialHeaderContainerHeight / 3) * 2, // two third of container height
								'min-height': 0
							});

							var y = initialHeaderContainerHeight - ((initialHeaderContainerHeight / 3) * 2);
							$('.main').css({
								transform: 'translate3d(0, -'+ y +'px, 0)',
								transition: 'ease transform 300ms'
							}).addClass('has-sticky-header-transform');

							if($html.hasClass('boxed')) {
								self.options.headerBody.css('position','fixed');
							}
						}

					}

					self.options.headerBody.css('top', self.options.stickySetTop);

					if (self.options.stickyChangeLogo) {
						self.changeLogo(true);
					}

					// Set Elements Style
					if( $('[data-sticky-header-style]').length ) {
						$('[data-sticky-header-style]').each(function() {
							var $el = $(this),
								css = theme.fn.getOptions($el.data('sticky-header-style-active')),
								opts = theme.fn.getOptions($el.data('sticky-header-style'));

							if( $window.width() > opts.minResolution ) {
								$el.css(css);
							}
						});
					}

					$.event.trigger({
						type: 'stickyHeader.activate'
					});
				};

				// Deactivate Sticky Header
				self.deactivateStickyHeader = function() {
					$html.removeClass('sticky-header-active');

					if ( $(window).width() < 992 && self.options.stickyEnableOnMobile == false) {
						return false;
					}

					// Sticky Effect - Shrink
					if( self.options.stickyEffect == 'shrink' ) {

						// Boxed Layout
						if( $html.hasClass('boxed') ) {

							// Set Header Body Position Absolute
							self.options.headerBody.css('position','absolute');

							if( $window.scrollTop() > $('.body').offset().top ) {
								// Set Header Body Position Fixed
								self.options.headerBody.css('position','fixed');								
							}

						} else {
							// Set Header Body Position Fixed
							self.options.headerBody.css('position','fixed');
						}

						// If Header Top
						if( self.options.wrapper.find('.header-top').get(0) ) {
							self.options.wrapper.find('.header-top').css({
								height: initialHeaderTopHeight,
								overflow: 'visible'
							});

							// Fix [data-icon] issue when first load is on middle of the page
							if( self.options.wrapper.find('.header-top [data-icon]').length ) {
								theme.fn.intObsInit( '.header-top [data-icon]:not(.svg-inline--fa)', 'themePluginIcon' );
							}
						}

						// Header Container
						self.options.wrapper.find('.header-container').css({
							height: initialHeaderContainerHeight
						});

					}

					self.options.headerBody.css('top', 0);

					if (self.options.stickyChangeLogo) {
						self.changeLogo(false);
					}

					// Set Elements Style
					if( $('[data-sticky-header-style]').length ) {
						$('[data-sticky-header-style]').each(function() {
							var $el = $(this),
								css = theme.fn.getOptions($el.data('sticky-header-style-deactive')),
								opts = theme.fn.getOptions($el.data('sticky-header-style'));

							if( $window.width() > opts.minResolution ) {
								$el.css(css);
							}
						});
					}

					$.event.trigger({
						type: 'stickyHeader.deactivate'
					});
				};

				// Always Sticky
				if (parseInt(self.options.stickyStartAt) <= 0) {
					self.activateStickyHeader();
				}

				// Set Logo
				if (self.options.stickyChangeLogo) {

					var $logoWrapper = self.options.wrapper.find('.header-logo'),
						$logo = $logoWrapper.find('img'),
						logoWidth = $logo.attr('width'),
						logoHeight = $logo.attr('height'),
						logoSmallTop = parseInt($logo.attr('data-sticky-top') ? $logo.attr('data-sticky-top') : 0),
						logoSmallWidth = parseInt($logo.attr('data-sticky-width') ? $logo.attr('data-sticky-width') : 'auto'),
						logoSmallHeight = parseInt($logo.attr('data-sticky-height') ? $logo.attr('data-sticky-height') : 'auto');

					if (self.options.stickyChangeLogoWrapper) {
						$logoWrapper.css({
							'width': $logo.outerWidth(true),
							'height': $logo.outerHeight(true)
						});
					}

					self.changeLogo = function(activate) {
						if(activate) {
							
							$logo.css({
								'top': logoSmallTop,
								'width': logoSmallWidth,
								'height': logoSmallHeight
							});

						} else {
							
							$logo.css({
								'top': 0,
								'width': logoWidth,
								'height': logoHeight
							});

						}
					}

					$.event.trigger({
						type: 'stickyChangeLogo.loaded'
					});

				}

				// Side Header
				var headerBodyHeight,
					flag = false;

				self.checkSideHeader = function() {
					if($window.width() < 992 && flag == false) {
						headerBodyHeight = self.options.headerBody.height();
						flag = true;
					}

					if(self.options.stickyStartAt == 0 && sideHeader) {
						self.options.wrapper.css('min-height', 0);
					}

					if(self.options.stickyStartAt > 0 && sideHeader && $window.width() < 992) {
						self.options.wrapper.css('min-height', headerBodyHeight);
					}
				}

				return this;
			},

			events: function() {
				var self = this;

				if( $(window).width() < 992 && this.options.stickyEnableOnMobile == false ) {
					return this;
				}

				if (!this.options.stickyEnableOnBoxed && $('body').hasClass('boxed') || $('html').hasClass('side-header-hamburguer-sidebar') && !this.options.stickyForce || !this.options.stickyEnabled) {
					return this;
				}

				if (!self.options.alwaysStickyEnabled) {
					$(window).on('scroll resize', function() {
						if ( $(window).width() < 992 && self.options.stickyEnableOnMobile == false) {
							self.options.headerBody.css({
								position: ''
							});

							if( self.options.stickyEffect == 'shrink' ) {
								self.options.wrapper.find('.header-top').css({
									height: ''
								});
							}

							self.deactivateStickyHeader();
						} else {
							self.checkStickyHeader();
						}
					});
				} else {
					self.activateStickyHeader();
				}

				$(window).on('load resize', function(){
					self.checkSideHeader();
				});

				$(window).on('layout.boxed', function(){
					self.boxedLayout();
				});

				return this;
			},

			boxedLayout: function(){
				var self = this,
					$window = $(window);

				if($('html').hasClass('boxed') && self.options.stickyEffect == 'shrink') {
					if( (parseInt(self.options.stickyStartAt) == 0) && $window.width() > 991) {
						self.options.stickyStartAt = 30;
					}

					// Set Header Body Position Absolute
					self.options.headerBody.css({
						position: 'absolute',
						top: 0
					});

					// Set position absolute because top margin from boxed layout
					$window.on('scroll', function(){
						if( $window.scrollTop() > $('.body').offset().top ) {
							self.options.headerBody.css({
								'position' : 'fixed',
								'top' : 0
							});								
						} else {
							self.options.headerBody.css({
								'position' : 'absolute',
								'top' : 0
							});
						}
					});
				}

				return this;
			}

		}

	});

}).apply(this, [window.theme, jQuery]);