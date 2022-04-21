# Pixapay-Fintech
Este repositório guarda algumas das documentações referentes a fintech [Pixapay](https://pixapay.com.br)!


### PIXAPAY PDV
- O aplicativo pixapay possui integração direta com qualquer PDV que desejar utilizá-lo. Por ele, é possível gerar cobranças de PIX instantâneo totalmente integrado.

Link oficial para download: https://pixapay.com.br/arquivos/Pixapay.zip

###### COMUNICAÇÃO
- O PDV que deseja integrar, precisa chamar o aplicativo "pixapay.exe" enviando de parâmetro o JSON abaixo demonstrado codificado em base64 ou gerar o json abaixo em um arquivo externo e enviar de parâmetro o caminho do arquivo que possui o json com a estrutura a seguir:

**Json de parâmetro**
```
  {
   "login":{
      "email":"seu@email.com",
      "password":"senhaDeAcesso"
   },
   "financeiro_conta_idpk": 1, /*Código que identifica qual é o caixa ou conta bancária que estaria sendo movimentada para relatórios futuros de conferência*/
   "sistema_idpk":1, /*Código único que identifica qual é o sistema que está sendo utilizado*/
   "empresa_idpk":1, /*Código único para identificar qual é a empresa que está sendo utilizada*/
   "filial_idpk":null, /*Caso este sistema for de uma filial, informa aqui seu código único*/
   "formVisible": true, /*Caso este parâmetro for true, exibirá a tela da fintech ao abrir o EXE. Caso for false, abrirá minimizado*/
   "callbackFilePath":"c:\\a\\", /*Caminho físico onde o pixapay gerará os arquivos de retorno sobre o processamento da cobrança PIX*/
   "charge":{
      "amountValue": 1500.50, /*Valor a ser gerada a cobrança pix*/
      "description":"Descrição para a cobrança" /*Descrição sobre a cobrança*/
   }
  }  
``` 

Exemplo de chamada enviando de parâmetro caminho do arquivo com a estrutura do json acima

``` 
//Abrir Pixapay via api SHELLEXECUTE do windows
    ShellExecute(
      Handle,
      'open',
      PChar('c:\pixapay\pixapay.exe'),
      PChar('c:\pixapay\params.json'),
      '',
      SW_SHOWMAXIMIZED
    );
``` 

Exemplo de chamada enviando de parâmetro o json acima informado convertido em base64

``` 
//Abrir Pixapay via api SHELLEXECUTE do windows
    ShellExecute(
      Handle,
      'open',
      PChar('c:\pixapay\pixapay.exe'),         
      PChar('ewogICAibG9naW4iOnsKICAgICAgImVtYWlsIjoic2V1QGVtYWlsLmNvbSIsCiAgICAgICJwYXNzd29yZCI6InNlbmhhRGVBY2Vzc28iCiAgIH0sCiAgICJmaW5hbmNlaXJvX2NvbnRhX2lkcGsiOiAxLAogICAic2lzdGVtYV9pZHBrIjoxLCAKICAgImVtcHJlc2FfaWRwayI6MSwKICAgImZpbGlhbF9pZHBrIjpudWxsLCAKICAgImNhbGxiYWNrRmlsZVBhdGgiOiJjOlxcYVxcIiwKICAgImNoYXJnZSI6ewogICAgICAiYW1vdW50VmFsdWUiOiAxNTAwLjUwLAogICAgICAiZGVzY3JpcHRpb24iOiJEZXNjcmnDp8OjbyBwYXJhIGEgY29icmFuw6dhIgogICB9CiAgfSA='),
      '',
      SW_SHOWMAXIMIZED
    );
``` 


- Feito isto, será aberto o EXE do pixapay com os dados enviados acima e exibindo o qrCode do pix para logo ser pago pelo integrador conforme imagem abaixo:
![image](https://user-images.githubusercontent.com/17827174/141384106-5bf2a65d-87f7-40cc-9db3-8bd57f625d15.png)

- Ao exibir a tela superior, o exe também salvará o qrCode em um arquivo local no caminho em que o usuário definiu nos parâmetros iniciais na propriedade **callbackFilePath** com nome "**pix.png**". Ao fazer isto, o PDV integrador terá a possibilidade de imprimir o qrCode também na impressora de bobina para exibir ao cliente que talvez não tenha acesso ao monitor em que o PDV está sendo executado para então conseguir efetuar o pagamento.

###### EXE MINIMIZADO
Quando a propriedade "formVisible" for "false", o EXE da fintech abrirá minimizado, então, quem ficará responsável em exibir o PIX para ser pago, será a aplicação integradora.
- 1- Nós fornecemos o acesso ao QRCode do pix gerado via arquivo local no caminho em que o usuário definiu nos parâmetros iniciais na propriedade **callbackFilepath"" com o nome "**pix.png**".
- 2- O código copia e cola do pix automaticamente ficará disponível no clipboard.

- 3-Disponibilizamos também um meio de o EXE integrador conseguir se comunicar com nossa aplicação via arquivo txt gerando um arquivo na pasta que foi definida em **callbackFilepath** com o nome **paramsPix.txt**:
- - 3.1- **Para cancelar o pix**, salve neste arquivo o valor "**1**" apenas;
- - 3.2- **Para imprimir na impressora de bobina**, salve neste arquivo o valor "**2**" apenas;
- - 3.3- **Para copiar o pix copia e cola para o clipboard**, salve neste arquivo o valor "**3**" apenas;


###### OPÇÕES DO USUÁRIO

- Caso usuário desejar cancelar esta cobrança, basta clicar no botão "Cancelar cobrança" ou no "X" de fechar na parte superior. Ao fazer isto, o pixapay gerará um arquivo json de retorno para seu PDV conseguir identificar que o usuário cancelou a geração de cobrança por pix. O nome do arquivo gerado é "**PixapayReturn.json**" e será salvo no caminho informado pelo json de parâmetro recebido na propriedade **callbackFilePath**.

**Json de cancelmaneto**
```
  {
   "status":"erro",
   "mensagem":"Cobrança cancelada."
  }
```

- Depois que foi efetivado o pagamento do pix, a tela logo se fechará retornando para o fluxo do PDV integrador. O pixapay.exe gerará o arquivo "**PixapayReturn.json**" na pasta em que foi informado nos parâmetros iniciais na propriedade **callbackFilePath**.

**Json de pagamento efetuado**
```
  {
   "status":"sucesso",
   "pix":{
      "fmp_idpk":258,
      "fmp_status":"Liquidado",
      "fmp_pagador_nome":"DOUGLAS COLOMBO",
      "fmp_pagador_banco":"BCO DO ESTADO DO RS S.A.",
      "fmp_pagador_conta":"3985072308",
      "fmp_pagamento_data":"11\/11\/2021 17:52:37",
      "fmp_pagador_agencia":"510",
      "fmp_pagador_documento":"04005135013",
      "fmp_pagador_conta_tipo":"PO",
      "fmp_cobranca_valor_recebido":2,
      "fmp_pagamento_transacao_id":"191BD57A-8771-E015-4164-D0DCE6DE92F8"
   }
  }
```

O PDV integrador precisará apenas:
- Chamar o executável do pixapay enviando os parâmetros documentados acima;
- Imprimir o qrCode na impressora de bobina (opcional);
- Aguardar o arquivo de retorno "**PixapayReturn.json**";
- Seguir o fluxo do PDV;
