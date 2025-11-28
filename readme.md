## 1. Sumario executivo

### Resumo Executivo da Avaliação de Segurança
A avaliação de segurança na infraestrutura da TechCorp Solutions (endereço 98.95.207.28) que revelou falhas críticas e sistêmicas,que podem resultar no comprometimento total do ambiente e de seus serviços internos.

A simulação de um ataque real demonstrou que a infraestrutura é vulnerável a um invasor com conhecimento intermediário. 
### Riscos Críticos Identificados
 * Exposição de Dados Críticos: Credenciais e senhas de banco de dados estavam armazenadas em arquivos de texto simples e logs de histórico dentro do sistema.

 * Controle Total do Serviço: Serviços de gerenciamento (FTP, SSH) foram configurados de forma insegura, permitindo que o atacante usasse privilégios de FTP para obter controle absoluto do sistema.

* Vulnerabilidade na Aplicação: Foi confirmada a ausência de sanitização de dados (validação de entrada), tornando a aplicação suscetível a ataques de SQLinjecion.

## 2. Metodologia e Escopo
A avaliação seguiu um modelo Black Box (simulação externa, sem conhecimento prévio da arquitetura interna) com um escopo de avaliar a perspectiva de um invasor externo (não autenticado), buscando a exploração e comprometimento total dos serviços expostos e da infraestrutura subjacente.
 ### Padrões de Referência
A execução do Pentest aderiu rigorosamente aos seguintes padrões para garantir abrangência e conformidade:
* OWASP (Open Web Application Security Project): Focando na identificação e classificação de vulnerabilidades web (Injection, Weak Credentials).

* PTES (Penetration Testing Execution Standard): Utilizado como guia para a estrutura geral da execução e relatórios.

## 3. Vulnerabilidades Relatadas
As prints a baixo mostra as vulnerabilidades que foram faladas de forma mais detalhada.

* ### Exposição de Diretórios Sensíveis via robots.txt
![robots 27/11/2025](/Prints/robot.png)
O arquivo robots.txt, embora primariamente destinado a guiar rastreadores de motores de busca, estava configurado incorretamente. Sua listagem continha entradas de exclusão que acidentalmente divulgaram e expuseram diretórios internos e sensíveis do servidor, violando o princípio de segurança por obscuridade.

* ### Credenciais em codigo
![database 27/11/2025 ](/Prints/database.png)
informações como senha e usuario no propio codigo a posse das credenciais do DB permitiram ao atacante realizar operações de sistema e escalar.

* ### SQLinjection no login
![admin 27/11/2025](/Prints/admin.png)
Usando um SQLinjection simples foi capaz de acessar o site como admin elevando os privilégios do atacante para o nível de Administrador

* ### SQL injection na busca
 ![injectionb 27/11/2025](/Prints/pesquisa%20inject.png)
     Alem do login tambem foi capaz de usar SQLinjection na busca.


* ### Armazenamento Inseguro de Credenciais em Texto Claro
![FTB 27/11/2025](/Prints/ftp.png)
Além de permitir o login anônimo, que facilitou a enumeração da estrutura de diretórios do servidor,
o FTP armazenava credenciais de alto privilégio em texto claro (usuários e senhas) em pastas e arquivos acessíveis.
<div>
<img src="/Prints/catpassword.png" alt="Cat do paswordtxt 27/11/2025" style="float: left; width: 45%; margin-right: 1%;">
    <img src="/Prints/catreadme.png" alt="Cat do readme 27/11/2025 " style="float: left; width: 50%;">
</div>
<img src=(/Prints/catwelcome.png) alt="cat do welcome 27/11/2025 "/>

* ### 
![SSH27/11/2025](/Prints/ssh.png)
![SSH INFORMAÇOES 27/11/2025](/Prints/ssh%20inf.png)
O ataque utilizo as informações obtidas do FTP o acesso ao serviço de SSH, que tambem possuia senhas e informações importantes.

## 4. Analise SWOT

* ### *Forças*
    * Serviço de FTP Presente: Oferece um canal de transferência de arquivos que, se corrigido, pode ser seguro.
    * Presença de SSH: Existe um canal criptografado para administração remota, embora tenha sido comprometido.
    * Uso de Controle de Versão (Git): A estrutura de desenvolvimento está sendo usada, mas de forma exposta.
* ### Fraquezas
   * Higiene de Segurança Ruim: Credenciais Hardcoded (em código) e armazenamento em texto claro (pastas com senhas).
   * Ausência de Sanitização de Dados (SQLi): Permitiu o bypass de autenticação com ataques triviais.
   * Exposição de Metadados Críticos: Diretório /.git acessível via HTTP, expondo código-fonte e históricos.
   * Configuração de Serviço Insegura: FTP permitiu acesso anônimo/leitura, facilitando a enumeração e a movimentação lateral.
* ### Oportunidades
    * Reforço de Segurança: Utilizar os achados do Pentest para priorizar o hardening imediato dos serviços (FTP, SSH) e do servidor Web.
    * implementação de WAF: Adotar um Web Application Firewall (WAF) para mitigar falhas como SQL Injection antes que cheguem ao código.
    * Automação de Deployment: Implementar uma pipeline de CI/CD (Continuous Integration/Continuous Deployment) para garantir que o .git nunca mais vá para produção.
    * Gestão de Credenciais: Adotar um Cofre de Senhas (Vault) para remover todas as credenciais em texto claro do código-fonte e do sistema de arquivos.
* ### Ameaças
   * Vazamento de Dados: Risco iminente de exposição de dados sensíveis de clientes e da própria empresa devido ao controle de Root.
   * Ataques Persistentes: O comprometimento do SSH (via FTP) permite a instalação de backdoors para garantir acesso futuro.
   * Penalidades Regulatórias: As falhas na proteção de credenciais e dados (SQLi) expõem a empresa a multas por não conformidade com a LGPD (Lei Geral de Proteção de Dados) ou outras regulamentações.
   * Ransomware e Destruição: O acesso de Root obtido pelo atacante possibilita a criptografia ou destruição total do servidor e dos dados

## 5. Conclusão e sugestões
 avaliação na infraestrutura da TechCorp Solutions (98.95.207.28) alcançou o Comprometimento Total (root) do sistema operacional e do banco de dados,a postura de segurança é classificada como CRÍTICA, devido à interconexão insegura dos serviços e falhas na camada de aplicação.
 * ### Sugestões
 Segurança da Aplicação: Eliminar a SQLi com Prepared Statements e Sanitização de Dados.

Higiene de Segurança: Remover todas as credenciais hardcoded e adotar um Cofre de Senhas (Secrets Vault).

Hardening: Desabilitar o acesso anônimo no FTP e remover a exposição do diretório /.git.