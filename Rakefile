# encoding: UTF-8
require 'handlebars'
require 'json'
require 'redcarpet'
require 'pdfkit'

def set_wkhtml_path(path)
  PDFKit.configure do |config|
    config.wkhtmltopdf = path
  end
end

task default: [:handlebars, :pdf] do
end

task :handlebars do
  handlebars = Handlebars::Context.new
  ['cp', 'cgv', 'cga'].each do |file|
    File.write(
      "tmp/#{file}.md",
      handlebars.compile(
        File.read("src/#{file}.md")
      ).call(
        JSON.parse(
          File.read('config.json').force_encoding('UTF-8')
        )
      )
    )
  end
end

task :pdf do
  # If you're having issues with wkhtmltopdf
  # uncomment this and remove the 'wkhtmltopdf-binary-edge' gem
  # set_wkhtml_path('/usr/local/bin/wkhtmltopdf')

  `mkdir -p contracts`

  ['cp', 'cgv', 'cga'].each do |file|
    `rm -f contracts/#{file}.pdf`

    markdown = Redcarpet::Markdown.new(Redcarpet::Render::HTML, autolink: true, tables: true)
    html = markdown.render(File.read("tmp/#{file}.md"))

    PDFKit.new(html, page_size: 'Letter').to_file("./contracts/#{file}.pdf")

    puts "generated file -> ./contracts/#{file}.pdf"
  end

  puts 'Success: Vos fichiers sont dans le dossier contracts'
end
