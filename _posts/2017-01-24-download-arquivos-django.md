---
layout: post
title: Download de arquivos dinâmicos com Django
date: 2017-01-24 00:00 -03:00
categories: backend django
---

Durante um projeto eu precisava que minha aplicação Django permitisse download de arquivos sem realmente escrever o mesmo no disco, para este fim, eu encontrei centenas de exemplos que me explicaram como fazer isto, e adaptei o mesmo para uso em conjunto com a biblioteca pyboleto, porém após exaustivas tentativas meu arquivo de retorno vinha somente com zero bytes, para resolver este problema, achei uma dica simples na api de arquivos do Python…


![Arquivo com tamanho zerado]({{ site.url }}/assets/images/2017-01-24-print.png)


Eu estava utilizando a api `io.BytesIO` para gerar o arquivo em tempo de execução e então retorná-lo via download, utilizando o mesmo método da documentação do django, porém o arquivo pdf era aberto no navegador porém com zero bytes (um simples print antes exibir mostrava que o arquivo era maior), então fui atrás de como o arquivo era escrito pela biblioteca `pyboleto`, percebi que a mesma utilizava o `reportlab`, e então percebi que após preencher todo o conteúdo da minha stream, o sistema não estava voltando o ponteiro da mesma, então por fim, acabei resolvendo este problema dando um simples `fileIO.seek(0)` o que resolveu o meu problema e pude então gerar meu boleto tranquilamente.
Então o código final ficou:

{% highlight python %}
fileIO = BytesIO()

boletoPDF = pdf.BoletoPDF(fileIO)
boletoPDF.drawBoleto(boleto_dados)
boletoPDF.save()

fileIO.seek(0)
response = HttpResponse(fileIO.read(), content_type='application/pdf')
response['Content-Dispositon'] = 'attachment; filename=boleto.pdf'

return response
{% endhighlight %}
