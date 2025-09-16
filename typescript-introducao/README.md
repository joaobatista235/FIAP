
```markdown
# 📊 Cadastro de Rankings

## ☑️ Contexto
Este documento descreve o processo para cadastrar novos rankings no sistema através da **API de integração**.  
O processo consiste em duas etapas principais:
1. Envio dos dados para cadastro.
2. Execução de um processo para efetivar a criação dos rankings.

---

## 🔄 O que mudou
Este é o **novo processo** para a criação de rankings, centralizado via integração.

---

## 📡 Como enviar os dados

A requisição para o cadastro da solicitação de criação de rankings deve ser feita via **POST** para o endpoint especificado.

### **Endpoint**
```

POST ws/integra/ws\_cadastro\_rankings.php

````

### **Parâmetros**

| Campo        | Obrigatório | Descrição                                                                 |
|--------------|-------------|----------------------------------------------------------------------------|
| `hash` (ou `sh`) | ☑ | Token de acesso para autenticação do usuário. |
| `sobrescrever`   | ☑ | Definido como `NAO`. |
| `dados_string`   | ☑ | String contendo os dados dos rankings. As colunas devem ser separadas por `;` e as linhas por `|`. |

---

## 📝 Exemplo de Envio

```json
{
  "sh": "SEU_TOKEN_DE_ACESSO_AQUI",
  "sobrescrever": "NAO",
  "dados_string": "ANO;MES;CDFILIAL;CDRANK;CDREST;COLOCSUPER;COLOCGER;COLUNA;DATACARGA;DTBASE;DTCARGA;MAXIMO;NMRANK;PERCGERAL;PONTOS;BOLA;TPPROC;COLOCGERAL;EXP_ORCADO;EXP_REAL;APURACAO|2024;09;4442;002;999;5;7;A;2024-11-29;2024-10-01;2024-11-01;100;RANK;95.5;80;50.75;P;10;1000;980;apuracao"
}
````

---

## 📑 Estrutura da `dados_string`

* **Primeira linha:** Cabeçalho com os nomes das colunas, separados por `;`.
* **Demais linhas:** Dados a serem inseridos, com valores separados por `;`.
* **Quebra de linhas:** Utilize `|` para separar diferentes linhas de dados.

---

## ⚙️ Executando a Criação dos Rankings

Após o envio da solicitação com os dados, é necessário executar o seguinte **cron** para que os rankings sejam efetivamente criados no sistema:

```
_cron/_cron_ws_cadastro_rankings.php?id_batch=35
```

---

## 👀 Visualizando os Rankings Cadastrados

Para que o card **"Rankings"** seja exibido na interface do sistema (dentro de um contrato), o seguinte registro deve estar presente no banco de dados:

```sql
INSERT INTO kpis (nome, descricao, codigo_sistema_externo) 
VALUES ("KPI tecfood", "Descricao", 2);
```

---

## ❗️ Pontos Importantes

* O envio dos dados **apenas cria uma solicitação**.
  A criação efetiva dos rankings depende da **execução do cron** especificado.

* Em caso de falha no processo de cadastro, uma notificação de erro pode ser enviada por e-mail.
  Para isso, execute o seguinte cron:

  ```
  _cron/_cronCrmEmails.php?id_batch=20000
  ```

---
