# Rake::FileUtilsExt.verbose(true)
require 'bundler'
require 'rake/clean'
require 'yaml'
require 'erb'
require_relative 'lib/emojistatic'
Bundler.require(:build)

##########################################################################
# Configuration
##########################################################################
DESIRED_SIZES = [64,32,24,20,16]

config_file = File.read('config.yml')
HOST = YAML.load(config_file)["host"]

##########################################################################
# Some helper methods
##########################################################################
def minify(target, source)
  file target => source do
    File.open(target,'w') do |f|
      puts "Minifying #{source} to #{target}"
      f.write( CSSMin.minify(File.read(source)) )
    end
  end
end

def gzipify(target, source)
  file target => source do
    sh "gzip -c #{source} > #{target}"
  end
end

##########################################################################
# Emoji images
##########################################################################
require_relative 'rakelib/images'

##########################################################################
# build embedded css sheets with data-uri
##########################################################################
require_relative 'rakelib/css_sheets'

##########################################################################
# build cache manifests
##########################################################################
require_relative 'rakelib/cache_manifests'

##########################################################################
# build emoji font family css
##########################################################################
require_relative 'rakelib/emojifont'


desc "resize copies of images to tmp directory for processing"
task :resize_images => EMOJI_SIZED_IMAGES
desc "created sized and optimized images of all individual emoji"
task :optimize_images => EMOJI_OPTIMIZED_IMAGES
desc "build cache manifests for emoji images"
task :cache_manifests => CACHE_MANIFESTS
desc "build css sheets for emoji images"
task :css_sheets => GZIP_CSS_SHEETS

CLOBBER.include('build/*')

task :build => [:optimize_images, :cache_manifests, :css_sheets]
task :default => :build

directory 'build/libs/js-emoji'
JSEMOJI_SRC = FileList['sources/js-emoji/emoji.{css,js}']
JSEMOJI_DST = JSEMOJI_SRC.pathmap('build/libs/js-emoji/%f')
# JSEMOJI_MIN = JSEMOJI_DST.pathmap('%X.min%x'')
CLOBBER.include('build/libs/js-emoji')


# namespace :assets do
#   namespace :images do
#     task :default => :all
#     task :all => [:copy, :optimize]

#     task :copy_to_tmp do
#       src = "sources/gemoji/images/emoji"
#       dst = "tmp/images/emoji"
#       FileUtils.cp_r src, dst  unless File.directory? dst
#     end
#     task :optimize => :copy_to_tmp do

#     end
#     task :copy_to_build do
#     end
#   end
#   task :spritesheets do
#   end
#   namespace :css_embedded_images do
#   end
# end

# namespace :libraries do
#   namespace :jsemoji do
#   end
# end
