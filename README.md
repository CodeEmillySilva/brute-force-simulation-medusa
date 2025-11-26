# brute-force-simulation-medusa

## ⚠️ Aviso Ético ⚠️

Este projeto é **exclusivamente educacional** e foi realizado em ambiente
controlado (Kali Linux + Metasploitable 2).  
Nunca utilize técnicas de força bruta ou varredura em sistemas reais sem
autorização explícita.

# Configuração do ambiente/máquinas utilizadas
- Kali Linux (Virtual Box - IP: 192.168.56.101)
- Metasploitable 2 (Virtual Box - IP: 192.168.56.102)
- Rede: Host-Only / Interna
  
# Wordlists utilizadas

users.txt:
* root
* msfadmin
* user
* test
* admin

wordlist.txt (nessa pesquisei as 10 senhas mais utilizadas do mundo e também adicionei msfadmin por saber que é a senha padrão do metasploitable):
* msfadmin
* 123456
* password
* qwerty123
* 111111
* 12345678
* secret
* admin123
* 1234
* 12345
* 123456789

# TESTE 1 - FTP

Comando utilizado: medusa -h 192.168.56.102 -U users.txt -P wordlist.txt -M ftp

Nesse comando, utilizando o medusa, que tem como objetivo fazer todas as combinações possíveis de usuário e senha disponíveis, mas nenhuma das senhas utilizadas na lista correspondia as credenciais. Logo nas linhas obtive apenas ACCOUNT CHECK, e não ACCOUNT FOUND, que aparecia caso alguma autenticação válida fosse encontrada.

# TESTE 2 - DVWA

Comecei acessando:

http://192.168.56.102/dvwa

Lá utilizei a senha e login disponibilizada na própria página inicial, entrei e coloquei a segurança em LOW (permitindo que ataques de força bruta funcionem sem proteções adicionais, deixando o teste mais didático no momento de aprendizagem).
Antes de dar continuidade entrei no código fonte dá página para verificar os nomes corretos dos campos: password, username, login e mensagem de erro.

Assim utilizei o seguinte comando:

hydra -L users.txt -P wordlist.txt 192.168.56.102 http-form-post "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:F=Login failed"

Todas as combinações foram testadas e por fim recebi a seguinte mensagem:
host: 192.168.56.102 (IP do meta) login:admin password:password
Indicando que o ataque de força bruta teve **sucesso**.

# TESTE 3 - SMB

Para testar o SMB, protocolo usado para compartilhamento de arquivos e pastas em rede, do Metasploitable utilizei o comando:

hydra -L users.txt -P wordlist.txt 192.168.56.102 smb

E obtive como retorno:

host: 192.168.56.102 (IP do meta) login:msfadmin password:msfadmin

Indicando que o ataque de password spraying, que tem como finalidade testar uma mesma senha para diversos usuários, teve **sucesso**.

# MEDIDAS DE MITIGAÇÃO

- Para FTP e SMB
* Usar senhas fortes (quantidades minima de caracteres, além de exigir combinação de letras maiúsculas, minúsculas, números e símbolos).
* Bloquear tentativas após uma certa quantidade
* Utilizar protocolos mais seguros, tendo em vista que eles terão uma maior cobertura ao incrementar criptografia, autenticação e integridade dos dados (protegendo um dos pilares da cibersegurança integridade, disponilibilidade e confidencialidade)

- Para Aplicações Web como a DVWA
* Autenticação multifator
* Utilizar HTTPS tendo em vista que há uma camada de segurança a mais, o que impede envio de credenciais sem criptografia.
* Limitar requisições por IP (Rate limiting)
