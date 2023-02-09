# Api-Testing-Usuarios-RobotFramework
Passo a passo para criação de API Testing com Robot framework.

	Para quem não sabe o que é uma API, comece por aqui!
	01 - O que é uma API:
	https://www.youtube.com/watch?v=tDNQBEc-au0

	02 – REST:
	https://becode.com.br/o-que-e-api-rest-e-restful/

	03 - Testando uma API:
	https://medium.com/@thi_carva/testando-servi%C3%A7os-web-api-com-postman-874ac81b20a3

1° Estruturar as requisições no API REST.
    1.1 No site https://robotframework.org/
		1.1.1 Clicar no button "RESOURCES"
		1.1.2 Clicar no button "LIBRARIES"
		1.1.3 No HTTP clicar no Name "RequestsLibrary", sendo essa Library para testar fazendo a comunicação com API REST.
		1.1.4 Sendo assim acessar a Keywords Documentation
		1.1.5 Assim vai para RequestsLibrary, para usar todas as ações para testar no VSCode.
	1.2 Criar uma pasta ou arquivo chamado RobotFramework dentro da pasta ApiTesting, com botão direito do mouser na pasta ApiTesting abrir o VSCode
    1.3 Criar 2 novos pastas e 2 novos arquivos  dentro do projeto APITESTING
		1.3.1  Criar pasta 1 nome "resources"
			1.3.1.1 Criar um arquivo com nome "api_testing_usuarios.resource"
		1.3.2  Criar pasta 1 nome "tests"
			1.3.1.2 Criar um arquivo com nome "api_testing_usuarios.robot"	
    1.4 Dentro do Projeto APITESTING na pasta tests e clicar no api_testing_usuarios.robot
        1.4.1 Abriar o editor de código 
		1.4.2 Escrever "*** Settings ***"
		1.4.3 Temos que declarar o resource "../resources/api_testing_usuarios.resource"
	1.5 Escrever seção "*** Variables ***"
	1.6 Criar a seção "*** Test Cases ***"
        1.6.1 Cenário 01: Cadastrar um novo usuário com sucesso na ServeRest
            1.6.1.1 Criar um usuário novo
            1.6.1.2 Cadastrar o usuário criado na ServeRest    email=${EMAIL_TESTE}    status_code_desejado=201
            1.6.1.3 Conferir se o usuário foi cadastrado corretamente

        1.6.2 Cenário 02: Cadastrar um usuário já existente
            1.6.2.1 Criar um usuário novo
            1.6.2.2 Cadastrar o usuário criado na ServeRest    email=${EMAIL_TESTE}    status_code_desejado=201
            1.6.2.3 Vou repetir o cadastro do usuário
            1.6.2.4 Verificar se a API não permitiu o cadastro repetido

        1.6.3 Cenário 03: Consultar os dados de um novo usuário
            1.6.3.1 Criar um usuário novo
            1.6.3.2 Cadastrar o usuário criado na ServeRest    email=${EMAIL_TESTE}    status_code_desejado=201
            1.6.3.3 Consultar os dados do novo usuário
            1.6.3.4 Conferir os dados retornados

        1.6.4 Cenário 04: Logar com o novo usuário criado
            1.6.4.1 Criar um usuário novo
            1.6.4.2 Cadastrar o usuário criado na ServeRest  email=${EMAIL_TESTE}  status_code_desejado=201
            1.6.4.3 Realizar Login com o usuário
            1.6.4.4 Conferir se o Login ocorreu com sucesso  
    1.7 Dentro do Projeto APITESTING na pasta resources e clicar no api_testing_usuarios.resource       
        1.7.1 Abriar o editor de código 
		1.7.2 Escrever "*** Settings ***"
		1.7.3 Utilizando e escrever "Library", dois clique ou clicando no TAB, sendo na frente da Library.
            1.7.3.1 Primeira linha escrever "ResquestsLibrary"
            1.7.3.1 Segunda linha escrever  "String"
    1.8 Criar a seção "*** Keywords ***"    
        1.8.1 Criar um usuário novo
            1.8.1.1 ${palavra_aleatoria}    Generate Random String    length=4    chars=[LETTERS]
            1.8.1.2 ${palavra_aleatoria}    Convert To Lower Case    ${palavra_aleatoria}
            1.8.1.3 Set Test Variable    ${EMAIL_TESTE}    ${palavra_aleatoria}@emailteste.com
            1.8.1.4 Log    ${EMAIL_TESTE}


        1.8.2 Cadastrar o usuário criado na ServeRest
            1.8.2.1 [Arguments]    ${email}    ${status_code_desejado}
            1.8.2.2 ${body}    Create Dictionary      
            1.8.2.3 ...        nome=Fulano da Silva     
            1.8.2.4 ...        email=${email}
            1.8.2.5 ...        password=1234    
            1.8.2.6 ...        administrador=true
            1.8.2.7 Log     ${body}

        1.8.3 Criar Sessão na ServeRest
            1.8.3.1 ${resposta}    POST On Session
            1.8.3.2 ...            alias=ServeRest
            1.8.3.3 ...            url=/usuarios
            1.8.3.4 ...            json=${body}
            1.8.3.5 ...            expected_status=${status_code_desejado}
            1.8.3.6 Log                  ${resposta.json()}
            1.8.3.7 IF        ${resposta.status_code} == 201
            1.8.3.8 Set Test Variable    ${ID_USUARIO}    ${resposta.json()["_id"]}
            1.8.3.9 END
            1.8.3.10 Set Test Variable    ${RESPOSTA}      ${resposta.json()}

        1.8.4 Criar Sessão na ServeRest
            1.8.4.1 ${headers}    Create Dictionary    accept=application/json    Content-Type=application/json
            1.8.4.2 Create Session    alias=ServeRest    url=https://serverest.dev    headers=${headers}

        1.8.5 Conferir se o usuário foi cadastrado corretamente
            1.8.5.1 Log    ${RESPOSTA}
            1.8.5.2 Dictionary Should Contain Item    ${RESPOSTA}    message    Cadastro realizado com sucesso
            1.8.5.3 Dictionary Should Contain Key     ${RESPOSTA}    _id

        1.8.6 Vou repetir o cadastro do usuário
            1.8.6.1 Cadastrar o usuário criado na ServeRest    email=${EMAIL_TESTE}    status_code_desejado=400

        1.8.7 Verificar se a API não permitiu o cadastro repetido
            1.8.7.1 Dictionary Should Contain Item    ${RESPOSTA}   message    Este email já está sendo usado

        1.8.8 Consultar os dados do novo usuário
            1.8.8.1 ${resposta_consulta}    GET On Session    alias=ServeRest    url=/usuarios/${ID_USUARIO}    expected_status=200
            1.8.8.2 Log    ${resposta_consulta.status_code}
            1.8.8.3 Log    ${resposta_consulta.reason}
            1.8.8.4 Log    ${resposta_consulta.headers}
            1.8.8.5 Log    ${resposta_consulta.elapsed}
            1.8.8.6 Log    ${resposta_consulta.text}
            1.8.8.7 Log    ${resposta_consulta.json()}
            1.8.8.8 Set Test Variable    ${RESP_CONSULTA}    ${resposta_consulta.json()}

        1.8.9 Conferir os dados retornados
            1.8.9.1 Log    ${RESP_CONSULTA}
            1.8.9.2 Dictionary Should Contain Item    ${RESP_CONSULTA}    nome             Fulano da Silva
            1.8.9.3 Dictionary Should Contain Item    ${RESP_CONSULTA}    email            ${EMAIL_TESTE}
            1.8.9.4 Dictionary Should Contain Item    ${RESP_CONSULTA}    password         1234
            1.8.9.5 Dictionary Should Contain Item    ${RESP_CONSULTA}    administrador    true
            1.8.9.6 Dictionary Should Contain Item    ${RESP_CONSULTA}    _id              ${ID_USUARIO}

        1.8.10 Realizar Login com o usuário
            1.8.10.1 ${body}  Create Dictionary
            1.8.10.2 ...      email=${EMAIL_TESTE}
            1.8.10.3 ...      password=1234  
    
            1.8.10.4 Criar Sessão na ServeRest

            1.8.10.5 ${resposta}  POST On Session
            1.8.10.6 ...          alias=ServeRest
            1.8.10.7 ...          url=/login
            1.8.10.8 ...          json=${body}
            1.8.10.9 ...          expected_status=200
    
            1.8.10.10 Set Test Variable    ${RESPOSTA}    ${resposta.json()} 

        1.8.11 Conferir se o Login ocorreu com sucesso
            1.8.11.1 Log  ${RESPOSTA}
            1.8.11.2 Dictionary Should Contain Item  ${RESPOSTA}  message  Login realizado com sucesso
            1.8.11.3 Dictionary Should Contain Key   ${RESPOSTA}  authorization
