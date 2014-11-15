require 'erb'
require 'liquid'
require 'tilt'

def gensvg(dot_filename)
  svg_filename = dot_filename.sub('.dot','.svg')
  `dot2svg #{dot_filename}`
  puts "    #{svg_filename} created from #{dot_filename}"
end

DEFAULT_DOT='enterprise-single.dot'
TEMPLATE_NAME='index.html.liquid'

# load the template for this file
def template(force=false)
  if $template.nil? || force
    $template = Tilt.new(TEMPLATE_NAME)
  end
  $template
end

# create an html file for a svg file
# dot_filename can be a svg or dt filename
def create_index(dot_filename)
  svg_filename = dot_filename.sub('dot','svg')
  index_filename = svg_filename.sub('svg','html')

  title = svg_filename.sub('.svg','').gsub(/[_-]/,' ').gsub(/(^| )(.)/) { "#{$1}#{$2.upcase}" }
  body = File.read(svg_filename)

  # remove xml version from the svg file
  body.gsub!(/^<\?.*\?>\n/,'')
  # remove DOCTYPE
  body.gsub!(/<!DOC[^>]*>\n/,'')

  # TODO:
  # embed images into svg document (via link?)
  #body.gsub('user.svg', '#user')

  index_contents = template.render binding, title: title, body: body
  File.write(index_filename, index_contents)
  puts "    #{index_filename} created from #{dot_filename}"
end

def reload_browser
  `osascript -e 'tell application "Google Chrome" to tell the active tab of its first window to reload'`
end

if $0 == __FILE__
  ARGV.each do |f|
    gensvg(f) if f =~ /.*\.dot/
    create_index(f) if f =~ /.*\.(svg|dot)/
  end
elsif $0.split("/").last == "guard"
  guard :shell do
    watch(DEFAULT_DOT) do |m| #/.*\.dot/
      puts "dot #{m[0]} has changed."
      gensvg(m[0])
      create_index(m[0])
      reload_browser
    end
    watch(TEMPLATE_NAME) do |m|
      puts "template #{m[0]} has changed."
      template(true) # reload the template
      create_index(DEFAULT_DOT)
      reload_browser
    end
    watch('style.css') do |m| # /.*\.csv/
      puts "css #{m[0]} has changed."
      reload_browser
    end
  end
end
