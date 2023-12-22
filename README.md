# Sistema de Cotação de Moedas
Este é um sistema simples de cotação de moedas desenvolvido em Python, utilizando a biblioteca Tkinter para a interface gráfica e a biblioteca Requests para realizar requisições à API AwesomeAPI.

## Funcionalidades
**1. Cotação de 1 Moeda Específica**
Seleção da Moeda: Escolha a moeda desejada a partir de uma lista de moedas disponíveis.
Seleção da Data: Utilize um calendário para escolher a data da cotação.
Obtenção da Cotação: Clique no botão "Pegar Cotação" para visualizar a cotação da moeda na data selecionada.

**2. Cotação de Múltiplas Moedas**
Seleção de Arquivo Excel: Selecione um arquivo Excel contendo as moedas na coluna A.
Seleção de Período: Escolha a data inicial e a data final para obter as cotações.
Atualização das Cotações: Clique no botão "Atualizar Cotações" para obter as cotações das moedas no período selecionado.

## Requisitos
Python 3.x
Bibliotecas: Tkinter, tkcalendar, pandas, requests, numpy
Instalação



# Comentários no Código
```
 #Cotação de 1 Moeda Específica
def pegar_cotacao():
    moeda = combobox_selecionarmoeda.get()
    data_cotacao = calendario_moeda.get()
    ano = data_cotacao[-4:]
    mes = data_cotacao[3:5]
    dia = data_cotacao[:2]
    link = f"https://economia.awesomeapi.com.br/json/daily/{moeda}-BRL/?start_date={ano}{mes}{dia}&end_date={ano}{mes}{dia}"
    requisicao_moeda = requests.get(link)
    cotacao = requisicao_moeda.json()
    valor_moeda = cotacao[0]['bid']
    label_textocotacao['text'] = f"A cotação da {moeda} no dia {data_cotacao} foi de: R${valor_moeda}"
```

> Este trecho do código realiza a cotação de uma moeda específica. Ele usa a biblioteca requests para fazer uma requisição à API AwesomeAPI, obtendo a cotação da moeda na data selecionada.

```
#Cotação de Múltiplas Moedas
def atualizar_cotacoes():
    try:
        # Ler o dataframe de moedas
        df = pd.read_excel(var_caminhoarquivo.get())
        moedas = df.iloc[:, 0]
        # Pegar a data de início e data de fim das cotações
        data_inicial = calendario_datainicial.get()
        data_final = calendario_datafinal.get()
        ano_inicial = data_inicial[-4:]
        mes_inicial = data_inicial[3:5]
        dia_inicial = data_inicial[:2]

        ano_final = data_final[-4:]
        mes_final = data_final[3:5]
        dia_final = data_final[:2]

        for moeda in moedas:
            link = f"https://economia.awesomeapi.com.br/json/daily/{moeda}-BRL/?" \
                   f"start_date={ano_inicial}{mes_inicial}{dia_inicial}&" \
                   f"end_date={ano_final}{mes_final}{dia_final}"

            requisicao_moeda = requests.get(link)
            cotacoes = requisicao_moeda.json()
            for cotacao in cotacoes:
                timestamp = int(cotacao['timestamp'])
                bid = float(cotacao['bid'])
                data = datetime.fromtimestamp(timestamp)
                data = data.strftime('%d/%m/%Y')
                if data not in df:
                    df[data] = np.nan

                df.loc[df.iloc[:, 0] == moeda, data] = bid
        df.to_excel("Teste.xlsx")
        label_atualizarcotacoes['text'] = "Arquivo Atualizado com Sucesso"
    except:
        label_atualizarcotacoes['text'] = "Selecione um arquivo Excel no Formato Correto"
```
> Neste trecho, o código atualiza as cotações de várias moedas em um intervalo de datas. Ele utiliza um arquivo Excel como entrada, lê as moedas, e para cada moeda, faz uma requisição à API AwesomeAPI para obter as cotações no período selecionado. Os resultados são atualizados em um DataFrame do pandas e exportados para um novo arquivo Excel.
