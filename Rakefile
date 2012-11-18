require 'net/http'

task :ping do
  Net::HTTP.get_response(URI.parse('http://trevorturk.com'))
end

require 'json'
require 'rest-client'
require 'yaml'

class Post
  def self.create(title, content=nil)
    path = "_posts/#{Date.today}-#{title.downcase.gsub(/[^[:alnum:]]+/, '-')}.markdown"

    if File.exist?(path)
      puts "[WARN] File exists - skipping create"
    else
      File.open(path, "w") do |file|
        file.puts YAML.dump({'layout' => 'post', 'layout' => 'type', 'published' => false, 'title' => title})
        file.puts "---"
        file.puts content if content
      end
    end

    path
  end
end

class Delicious
  def self.links
    yaml = YAML.load_file('_private.yml')
    user = yaml['delicious_user']
    pass = yaml['delicious_pass']
    resp = RestClient.get("https://#{user}:#{pass}@api.del.icio.us/v1/json/posts/all?results=500")
    json = JSON.parse(resp)['posts']

    String.new.tap do |str|
      json.each do |post|
        href = post['post']['href']
        desc = post['post']['description']
        extd = post['post']['extended'] || nil

        str << %(<p><a href="#{href}">#{desc}</a></p>\n)
        str << %(<blockquote><p>#{extd.chomp}</p></blockquote>\n\n) if extd
      end
    end
  end
end

task :post do
  ARGV.shift
  title = ARGV.join(' ')
  path = Post.create(title)
  `subl #{path}`
  exit 1
end

task :links do
  path = Post.create("Links for #{Time.now.strftime('%F')}", Delicious.links)
  `subl #{path}`
  exit
end
