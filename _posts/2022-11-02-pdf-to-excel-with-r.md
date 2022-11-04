## Convertendo informações em PDF para o Excel com a linguagem R

Nesse post vamos descobrir como transformar dados de arquivos em PDF para uma tabela em Excel, utilizando a linguagem R. A motivação é simples: dados disponibilizados em planilhas são muito mais fáceis de analisar e explorar.

Para tal, vamos (i) ler os dados do [edital do concurso público da PETROBRAS][edital-url], de 12/2021, com o objetivo de obter as disciplinas para o cargo de Cientista de Dados, (ii) arrumar os dados (`tidy data`)  e (iii) exportar em .xlsx.

![Página 35 do edital](/img/00_post/img0.png)

Começamos lendo o pdf com o pacote ‘pdftools‘ e exibimos na tela a primeira página do edital.

```r
edital = ("data-raw/PETROBRAS_edital_de_abertura_n_1.pdf")
txt <- pdftools::pdf_text(pdf=edital)
cat(txt[1]) 
#=> # Exibe a primeira página do documento.
```

![Saída gerada no console do RStudio](/img/00_post/img1.png)

Inspecionando o edital, é possível localizar as informações desejadas nas páginas 35 e 36. Utilizando os pacotes ‘readr‘ e ‘stringr‘ transformamos cada linha do PDF em um elemento de um vetor e removemos todos os espaço extras entre as palavras. O contéudo do cargo de cientista de dados começa apenas na 28ª linha da página 35 do edital.

```r
#=> # Página de interesse 35 e 36.
page_35 = txt[35] |> readr::read_lines()
page_36 = txt[36] |> readr::read_lines()

ds1 = page_35[28:52] |> stringr::str_squish()
ds2 = page_36[6:16] |> stringr::str_squish()
```

A seguir, vamos transformar todos os elementos desse vetor em um único elemento, contemplando todas as linhas de interesse do pdf.

```r
n = length(ds1)

init_ = paste(ds1[1], ds1[2], sep=" ")

for (i in 3:n) {
  init_ = paste(init_,ds1[i], sep=" ")
}
```

O próximo passo é, novamente, separar esse vetor em diferentes elementos, porém agora com uma separação diferente. Queremos que cada disciplina seja uma linha da tabela. Nos aproximamos agora do conceito ‘tidy data’ tão bem apresentado por [Hadley Wickam, 2014][paper-url].

```r
tab = init_ |> stringr::str_split("\\. ") 

tab_final = tibble::as_tibble(tab[[1]])
```

As linhas de código que acabamos de escrever para página 35, também devem ser repetidas para a página 36. Com apenas duas páginas não é difícil copiar e colar o código novamente, correto? Mas, e se tivéssemos que repetir esse mesmo processo de copy and paste dezenas de vezes? Nesse caso, uma forma mais eficiente é definir uma função, alterando apenas os seus parâmetros quando necessário.

```r
arrumar_dados = function(text){
  
  n = length(text)
  
  init_ = paste(text[1], text[2], sep=" ")
  
  for (i in 3:n) {
    init_ = paste(init_,text[i], sep=" ")
  }
  
  init_ = init_ |> stringr::str_split("\\. ")
  
  tab = tibble::as_tibble(init_[[1]])
  
  return(tab)
  
}

tab_1 = arrumar_dados(text=ds1)

tab_2 = arrumar_dados(text=ds2)

tab_final = dplyr::bind_rows(tab_1, tab_2)

```

Por fim, vamos acertar a primeira linha da tabela, separando o título do subtítulo. A primeira linha da tabela é "ÊNFASE 7: CIÊNCIA DE DADOS – BLOCO I: 1 Aprendizado supervisionado: Regressão e Classificação".

```r
title_subt = tab_final[1,1] |> dplyr::pull(value)

title = title_subt |> stringr::str_sub(start = 1L, end = 26L)
subtitle = title_subt |> stringr::str_sub(start = 30L, end = -1L)

tab_final[1,1] = subtitle

colnames(tab_final) = title
```

Agora já podemos exportar para Excel.

```r
tab_final |> writexl::write_xlsx('Disciplinas_DS_Petrobas.xlsx')
```

Com pouquíssimas linhas de código, extraímos informações relevantes de um PDF, organizamos nossos dados e exportamos para Excel.

Repositório do projeto no GitHub: [pdf_to_xlsx][wfaquieri-gh] 

[edital-url]: https://petrobras.com.br/data/files/C4/B4/F8/D2/528CD710722C4CD7B8E99EA8/Ed_1_Petrobras_PSP1_2021_abertura.pdf
[wfaquieri-gh]: https://github.com/wfaquieri/pdf_to_xlsx
[paper-url]: https://www.jstatsoft.org/article/view/v059i10
