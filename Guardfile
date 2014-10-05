require 'erb'
#require 'hpricot'

def gensvg(file_name)
  `dot2svg #{file_name}`
  puts "    #{file_name.sub('.dot','.svg')} created from #{file_name}"
end

DEFAULT_DOT='enterprise-single.dot'
TEMPLATE_NAME='index.html.erb'

def template(force=false)
  if $template.nil? || force
    $template = ERB.new(File.read(TEMPLATE_NAME))
  end
  $template
end

def create_index(dot_filename) # this may be dot or svg
  svg_filename = dot_filename.sub('dot','svg')
  index_filename = svg_filename.sub('svg','html')

  body = File.read(svg_filename)
  body.gsub!(/^<\?.*\?>\n/,'')
  body.gsub!(/<!DOC[^>]*>\n/,'')

  # TODO:
  #body.gsub('user.svg', '#user')

  index_contents = template.result(binding)
  File.write(index_filename, index_contents)
  puts "    #{index_filename} created from #{dot_filename}"
end

def reload_browser
  `osascript -e 'tell application "Google Chrome" to tell the active tab of its first window to reload'`
end

guard :shell do
  watch(DEFAULT_DOT) do |m| #/.*\.dot/
    puts "dot #{m[0]} has changed."
    gensvg(m[0])
    create_index(m[0])
    reload_browser
  end
  watch(TEMPLATE_NAME) do |m|
    puts "erb #{m[0]} has changed."
    template(true) # reload the template
    create_index(DEFAULT_DOT)
    reload_browser
  end
  watch('style.css') do |m|
    puts "css #{m[0]} has changed."
    reload_browser
  end
end
