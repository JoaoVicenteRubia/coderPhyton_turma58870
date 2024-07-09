# Sistema de Consulta e Notificação de Corretoras

Este projeto consulta informações de corretoras utilizando a API pública do BrasilAPI. Ele cria três tabelas diferentes com essas informações e implementa um sistema de notificação que alerta o usuário caso o CNPJ consultado esteja inativo.

## Funcionalidades

- Consulta de informações de corretoras utilizando a API pública do BrasilAPI.
- Criação de três tabelas com informações detalhadas das corretoras.
- Sistema de notificação que alerta o usuário caso o CNPJ consultado esteja inativo

## 🎯 Instruções

O projeto foi construído para fins de desenvolvimento e teste.

## 📋 Pré-requisitos

Para o desenvolvimento do projeto, foram instaladas algumas bibliotecas:

datetime (inclusa na biblioteca padrão do Python)
plyer
pandas
requests

Para instalar as dependências, execute:

!pip install pandas requests plyer

## ⚙️ Testes
Para testar o projeto:

     1 Clone o repositório:

  git clone https://github.com/JoaoVicenteRubia/coderPhyton_turma58870.git cd coderPhyton_turma58870

     2 Execute o arquivo principal:

  python projeto_corretoras.py

      3 Siga as instruções no terminal para consultar a situação de um CNPJ específico.

## 🛠️ Construído com

Python - Linguagem de programação - Versão: 3.10.6
Pandas - Biblioteca Python para manipulação e análise de dados
Requests - Biblioteca Python para realizar requisições HTTP
Plyer - Biblioteca Python para notificações

# Uso

## Exemplo Completo

import pandas as pd
import requests
from datetime import datetime
from plyer import notification

# Código principal para consultar e exibir informações sobre corretoras
url = "https://brasilapi.com.br/api/cvm/corretoras/v1"
response = requests.get(url)

if response.status_code == 200:
    data_json = response.json()
else:
    print("Erro ao acessar a API")

# Criar DataFrame com as informações das corretoras
corretoras = pd.DataFrame(data_json)
print(corretoras.head(4))

CNPJ = [corretoras['cnpj'] for corretoras in data_json]
social = [corretoras['nome_social'] for corretoras in data_json]
comercial = [corretoras['nome_comercial'] for corretoras in data_json]
cidade = [corretoras['municipio'] for corretoras in data_json]
cep = [corretoras['cep'] for corretoras in data_json]
pais = [corretoras['pais'] for corretoras in data_json]
estado = [corretoras['uf'] for corretoras in data_json]

nomes_corretoras = pd.DataFrame({
    'CNPJ': CNPJ,
    'Nome social': social,
    'Nome comercial': comercial,
    'Cidade': cidade,
    'Estado': estado,
    'Pais': pais
})
print(nomes_corretoras.head(10))

situação = [corretoras['status'] for corretoras in data_json]
atual = [corretoras['data_inicio_situacao'] for corretoras in data_json]
inicio = [corretoras['data_registro'] for corretoras in data_json]

situação_corretoras = pd.DataFrame({
    'Nome social': social,
    'Situação da empresa': situação,
    'Data Inicio da empresa': inicio,
    'Data da ultima atualização': atual
})
print(situação_corretoras.head(10))

patrimonio = [corretoras['valor_patrimonio_liquido'] for corretoras in data_json]
data_patrimonio = [corretoras['data_patrimonio_liquido'] for corretoras in data_json]

corretoras_patrimonio = pd.DataFrame({
    'Nome social': social,
    'Situação da empresa': situação,
    'Valor do Patrimonio': patrimonio,
    'Data do ultimo Patrimonio': data_patrimonio
})
print(corretoras_patrimonio.head(10))

# Função de notificação
def alerta(titulo, mensagem):
    notification.notify(
        title=titulo,
        message=mensagem,
        app_name='Sistema de Notificação',
        timeout=10
    )

# Função para consultar a situação do CNPJ
def consultar_situação(cnpj):
    try:
        response = requests.get(f'https://brasilapi.com.br/api/cvm/corretoras/v1/{cnpj}')
        response_data = response.json()
        
        status = response_data.get('status')
        
        if status == 'CANCELADA':
            data_atual = datetime.now().strftime("%d/%m/%Y %H:%M:%S")
            mensagem = f"CNPJ {cnpj} cancelado\nData: {data_atual}"
            alerta('CNPJ Cancelado', mensagem)
            print(f"CNPJ {cnpj} cancelado")
        else:
            print(f"CNPJ {cnpj} ativo: {status}")
    except Exception as e:
        print(f'Erro ao consultar CNPJ: {e}')

if __name__ == '__main__':
    cnpj = input("Digite o CNPJ: ")
    consultar_situação(cnpj)
    
## ✒️ Autor
João Rubia Romeiro De Vita






