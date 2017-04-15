define(['jquery', 'internal/sitebuilder/common/ModuleClassLoader', 'translate!webs.module.carousel'],
function($, ModuleClassLoader, translate) {
	var module = {}, extend = {};

	// SubModules
		extend.submodules = {"button":{"moduleType":"button"}};

	// Module Styles
	extend.styles = {"default":{"global":{"css":"plain.view.less","js":"plain.view.js"},"default":true,"slug":"default"}};
	extend.styles['default']['global']['js'] = (function(module, extend){
		

/* plain.view.js */

		return module;
	});
	extend.defaultStyle = extend.styles['default'];

	// View JS
	if(webs && webs.theme && webs.theme.mobile) {
	var BURNS_DURATION = 6000,
		FADE_DURATION = 2000,
		SLIDE_DURATION = BURNS_DURATION - FADE_DURATION,
		$html,
		useCssAnim;

	$.extend(module, {
		oneLoaded: function() {
			var self = this,
				data = this.data;

			$html = $('html');
			useCssAnim = $html.hasClass('cssanimations');

			jQuery.fx.interval = 33;

			this.activeIndex = 0;

			this.$carousel = this.el.find('.w-carousel');
			this.$list = this.$carousel.find('ul.w-carousel-list');

			this.$indices = this.$carousel.find('.w-carousel-indices').children();

			this.$list.children().each(function() {
				var s = $(this),
					img = s.find('img'),
					src = img.attr('src');
					if(!src) {
						s.remove();
					}
			});

			this.slides = this.$list.children();

			this.count = this.slides.length;
			this.width = this.$carousel.width();

			if(this.count === 0) {
				this.$carousel.addClass('empty');
				return;
			} else {
				this.$carousel.addClass('populated');
			}

			this.slides.each(function(){
				var slide = $(this),
					image = slide.find('img'),
					mobileImage = image.clone(true,true);

				mobileImage.removeAttr('style');
			});

			this.pushCCInfo();

			if(this.count > 1) this.play();
		},
		play: function() {
			var self = this;
			this.activeSlide = this.$list.children().eq(this.activeIndex);
			this.animate(this.activeSlide);
			setInterval(function(){
				self.nextSlide();
			}, SLIDE_DURATION);
		},
		animate: function(slide) {
			var rand1 = Math.round(Math.random()*100),
				rand2 = Math.round(Math.random()*100),
				origin = rand1+'% '+rand2+'%',
				randTop = Math.round(Math.random()*(Math.max(0,slide.find('img').height()-120)));

			slide.addClass('active');
			if(useCssAnim) {
				slide.find('img').css({
					'-moz-transform-origin': origin,
					'-ms-transform-origin': origin,
					'-webkit-transform-origin': origin,
					'transform-origin': origin,
					'top': '-'+randTop+'px'
				}).addClass('burns');
			} else {
				require([webs.props.staticServer + '/active-static/lib/jquery.kenburns-0.1.js'], function jsAnimate() {
					slide.find('img').kenBurns({
						width: this.width,
						height: 120,
						duration: BURNS_DURATION
					});
				});
			}
		},
		getNext: function() {
			var nextIndex = this.activeIndex + 1;
			var nextSlide = this.activeSlide.next();
			if(nextSlide.length === 0) {
				nextIndex = 0;
				nextSlide = this.$list.children().eq(nextIndex);
			}
			return {
				index: nextIndex,
				slide: nextSlide
			};
		},
		fadeOut: function(slide) {
			var z = slide.css('z-index');

			slide.css('z-index','1000').fadeOut(FADE_DURATION,function(){
				slide.removeClass('active').removeAttr('style').css('z-index',z);
			});
		},
		nextSlide: function() {
			var next = this.getNext();

			this.fadeOut(this.activeSlide);
			this.animate(next.slide);

			this.activeIndex = next.index;
			this.activeSlide = next.slide;
		},

		pushCCInfo: function(){
			var slides = this.data.slides;
			for(var i=0,len=slides.length;i<slides.length;i++) {
				var image = slides[i].image;
				if(image.imageType == 'flickr') {
					try {
						webs.page.addCCImage({
							title: image.flickr.title,
							artist: image.flickr.ownerName,
							url: 'http://www.flickr.com/photos/' + image.flickr.ownerId + '/' + image.flickr.photoId
						},'#outer .container','click touch');
					} catch(ex) {
						webs.log.error('Unable to create ccImage', image, ex);
					}
				}
			}
		}
	});
} else {
	require(["internal/sitebuilder/modules/common/transitions"]);
	$.extend(module, {

		events: {
			'click .w-carousel-prev':  'previousSlide',
			'click .w-carousel-next':  'nextSlide',
			'click .w-carousel-index': 'indexClick'
		},

		oneLoaded: function() {
			var self = this, data = this.data;

			this.activeSlide = 0;				// current slide's index

			this.$carousel = this.el.find('.w-carousel');
			this.$list = this.$carousel.find('ul.w-carousel-list');
			this.$indices = this.$carousel.find('.w-carousel-indices').children();
			this.slides = this.$list.children();
			this.count = this.slides.length;

			this.pushCCInfo();

			window.temp = $('#webs-header').trigger('slidesLoaded');

			if(this.data.autoplay && this.count > 1) this.play();
		},

		play: function() {
			var self = this;
			setInterval(function(){ self.nextSlide(); }, this.data.speed);
		},

		indexClick: function(e) {
			this.selectSlide(parseInt($(e.target).text(), 10) -1);
			return false;
		},

		selectSlide: function(i) {
			if(i===-1) i+=this.count;
			else if(this.count=== i) i = 0;

			if(this.transitioning) return false;

			var
			self = this,
			oldEl = this.slides.eq(this.activeSlide).addClass('outgoing'),
			newEl = this.slides.eq(i).addClass('incoming'),
			oldi = this.activeSlide,
			transition = webs.transitions[this.data.transition] || webs.transitions.none,
			invertTransition = (i < oldi) && !(i === 0 && oldi === this.count-1) ||
				(i === this.count-1 && oldi === 0);

			this.$carousel.addClass("w-carousel-transitioning");

			this.activeSlide = i;
			this.transitioning = true;

			transition(oldEl, newEl, this.data.duration, invertTransition, function(){
				self.transitioning = false;
				oldEl.removeClass("outgoing active");
				newEl.removeClass("incoming").addClass("active");
				self.$carousel.removeClass("w-carousel-transitioning");

				self.$indices.eq(oldi).removeClass("active");
				self.$indices.eq(i).addClass("active");
			});

			self.el.trigger('slideSelected', self);
		},
		previousSlide: function() { this.selectSlide(this.activeSlide-1); return false; },
		nextSlide: function() { this.selectSlide(this.activeSlide+1); return false; },

		describeSubmodules: function() {
			var self = this,
				buttons = self.el.find(".w-carousel-button");
				submoduleDescriptions = [];
			var count = 0;
			$.each(buttons, function(buttonIndex) {
				if (self.data.slides.length <= buttonIndex) return;
				submoduleDescriptions.push({
					name: "button" + buttonIndex,
						el: buttons.eq(buttonIndex).find(".webs-submodule"),
						slug: "button",
						data: self.data.slides[buttonIndex].button
				});
			});
			return submoduleDescriptions;
		},

		pushCCInfo: function(){
			var slides = this.data.slides;
			for(var i=0,len=slides.length;i<slides.length;i++) {
				var image = slides[i].image;
				if(image.imageType == 'flickr') {
					try {
						webs.page.addCCImage({
							title: image.flickr.title,
							artist: image.flickr.ownerName,
							url: 'http://www.flickr.com/photos/' + image.flickr.ownerId + '/' + image.flickr.photoId
						});
					} catch(ex) {
						webs.log.error('Unable to create ccImage', image, ex);
					}
				}
			}
		}
	});
}


	return ModuleClassLoader.register('carousel', module, extend);
});