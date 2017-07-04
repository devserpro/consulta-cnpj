# API Consulta CNPJ

API para acesso ao Cadastro de Pessoas Físicas diretamente das bases da Receita Federal do Brasil.

A plataforma APIGOV (Plataforma que contempla todas as API's disponibilizadas e comercializadas pelo SERPRO) utiliza o protocolo Oauth2 - Client Credential Grant ([https://tools.ietf.org/html/rfc6749#section-4.4](https://tools.ietf.org/html/rfc6749#section-4.4)) para realizar a autenticação e autorização de acesso para consumo das API's contratadas, conforme figura abaixo:

<img title="Processo de autenticação e autorização APIS" src="https://raw.githubusercontent.com/devserpro/consulta-cpf/master/img/oauth.png" style="width=50%;" />

## Como fazer consultas a API CNPJ

Para consumir a API Consulta CNPJ, você deverá utilizar os dois códigos (Consumer Key e Consumer Secret) disponibilizados na Área do Cliente. Esses códigos servem para identificar o contrato e deverão ser informados sempre que uma consulta for realizada.
Exemplos de códigos:

**Consumer Key**: uldY78ZMvYm4btC0x3XZLG7ZTsYa

**Consumer Secret**: WyUeBFCUK7wu1Ko61V7bb7yB2Uoa

### 1 – Como solicitar o Token de Acesso (Bearer)
Para consultar a API, é necessário obter um token de acesso temporário (Bearer). Esse token possui um tempo de validade e sempre que expirado, este passo de requisição de um novo token de acesso deve ser repetido. 

Para solicitar o token temporário é necessário realizar uma requisição HTTP POST para o endpoint Token https://apigateway.serpro.gov.br/token, informando as credenciais de acesso(consumerKey:consumerSecret) no HTTP Header Authorization, no formato base64, conforme exemplo abaixo. As credenciais de acesso devem ser obtidas a partir do portal do cliente Serpro - https://minhaconta.serpro.gov.br

```
[POST] grant_type=client_credentials
[HEAD] Authorization: Basic base64(Consumer Key:Consumer Secret)
```

Abaixo segue um exemplo de chamada via cUrl:

```curl
curl -k -d "grant_type=client_credentials" -H "Authorization: Basic dWxkWTc4Wk12WW00YnRDMHgzWFpMRzdaVHNZYTpXeVVlQkZDVUs3d3UxS282MVY3YmI3eUIyVW9h" https://apigateway.serpro.gov.br/token
```

A chave informada no exemplo acima "dWxkWTc4Wk12WW00YnRDMHgzWFpMRz
daVHNZYTpXeVVlQkZDVUs3d3UxS282MVY3YmI3eUIyVW9h" é resultado do BASE64 dos códigos Consumer Key e Consumer Secret separados pelo caracter “:”, conforme exemplo a seguir:

```curl
base64(uldY78ZMvYm4btC0x3XZLG7ZTsYa:WyUeBFCUK7wu1Ko61V7bb7yB2Uoa)
```

**Receba o Token**

Como resultado, o endpoint informará o token de acesso a API, no campo access_token da mensagem json de retorno. Este token deve ser informado nos próximos passos.

```json
{"scope":"am_application_scope default","token_type":"Bearer","expires_in":3295,"access_token":"c66a7def1c96f7008a0c397dc588b6d7"}
```

**Renovação do Token de Acesso**

Atentar que sempre que o token de acesso temporário expirar, o gateway vai retornar um _HTTP CODE 401_ após realizar uma requisição para uma API. Neste caso, deve ser repetido o passo anterior (**Como solicitar o Token de Acesso (Bearer)**) para geração de um novo token de acesso temporário.


### 2 – Como realizar a consulta à API

De posse do Token de Acesso, faça uma requisição via GET ao gateway informando os parâmetros da API. Exemplo:

```curlBearer
curl -X GET --header "Accept: application/json" --header "Authorization: Bearer c66a7de41c96f7008a0c397dc588b6d7" "https://apigateway.serpro.gov.br/consulta-cnpj/v1/cnpj/99999999999999"
```

No exemplo acima foram utilizados os seguintes parametros:

**[HEADER] Accept: application/json** - Informamos o tipo de dados que estamos requerendo, nesse caso JSON

**[HEADER] Authorization: Bearer <span class="bearer">c66a7de41c96f7008a0c397dc588b6d7</span>** - Informamos o token de acesso recebido

**[GET] https://apigateway.serpro.gov.br/consulta-cnpj/v1/99999999999999**: chamamos a url da API informando o CNPJ. No caso a url é "consulta-cnpj/v1/cnpj/{numero do CNPJ}"

Nesse caso, espera-se que a resposta seja a seguinte:

```json
{
  "ni": "99999999999999",
  "data_abertura": "2006-03-06",
  "nome_empresarial": "EMPRESA DE SOFTWARE FICTICIA",
  "nome_fantasia": "EMPRESA FICTICIA",
  "cnae_principal": {
    "codigo": "6201501",
    "descricao": "Desenvolvimento de programas de computador sob encomenda"
  },
  "natureza_juridica": {
    "codigo": "2062",
    "descricao": "Sociedade Empresária Limitada"
  },
  "endereco": {
    "logradouro": "QUADRA FICTICIA COMERCIAL",
    "numero": "12",
    "complemento": "Ed. Gomes Ficticio",
    "cep": "70000000",
    "bairro": "SETOR FICTICIO",
    "municipio": "BRASILIA",
    "uf": "DF"
  },
  "situacao_especial": "",
  "situacao_cadastral": {
    "codigo": "2",
    "data": "2007-02-07",
    "motivo": ""
  },
  "orgao": "0110100",
  "qt_comprovante": "",
  "qt_certidao": "",
  "tipo_estabelecimento": "1",
  "correio_eletronico": "emailficticio@emailficticio.com.br",
  "capital_social": 2800000,
  "porte": "03",
  "telefones": [
    {
      "ddd": "61",
      "numero": "55559999"
    }
  ],
  "cnae_secundarias": [
    {
      "codigo": "6209100",
      "descricao": "Suporte técnico, manutenção e outros serviços em tecnologia da informação*"
    },
    {
      "codigo": "6319400",
      "descricao": "Portais, provedores de conteúdo e outros serviços de informação na internet*"
    },
    {
      "codigo": "6399200",
      "descricao": "Outras atividades de prestação de serviços de informação não especificadas anteriormente*"
    }
  ],
  "nome_orgao": "BRASILIA",
  "ente_federativo": "",
  "socios": [
    {
      "nome": "JOSÉ ROBERTO GOMES FICTICIO",
      "qualificacao": "49",
      "captalSocial": "00000000933400",
      "codigoPais": "000",
      "qualificacaoRepLegal": "",
      "nomeRepLegal": ""
    },
    {
      "nome": "JOSÉ FILHO GOMES FICTICIO",
      "qualificacao": "49",
      "captalSocial": "00000000933300",
      "codigoPais": "000",
      "qualificacaoRepLegal": "",
      "nomeRepLegal": ""
    },
    {
      "nome": "CAROLINA GOMES FICTICIA",
      "qualificacao": "49",
      "captalSocial": "00000000933300",
      "codigoPais": "000",
      "qualificacaoRepLegal": "",
      "nomeRepLegal": ""
    }
  ]
}
```

