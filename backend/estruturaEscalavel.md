````ts
import express, { type Application, type RequestHandler } from "express";
import config from "../config/env.js";
import type { ServRouter } from "../interface/index.js";

/**
 * Interface para opções de configuração do servidor.
 * Permite injeção de dependências para middlewares, rotas e outras configurações.
 */
export interface ServerOptions {
  /** Porta em que o servidor irá escutar. Se não fornecida, usa a porta do config. */
  port?: number;
  /** Middlewares globais a serem aplicados na aplicação Express. */
  middlewares?: RequestHandler[];
  /** Rotas a serem registradas no servidor. */
  routers?: ServRouter[];
  /** Se true, aplica middlewares padrão (json, urlencoded). */
  useDefaultMiddlewares?: boolean;
}

/**
 * Classe base que representa um servidor Express configurável.
 *
 * Esta classe encapsula a lógica de configuração e gerenciamento de um servidor Express,
 * mantendo a separação entre a configuração do app e a inicialização do servidor.
 *
 * Por que separar em classe base?
 * - Reutilização: permite criar diferentes tipos de servidores a partir de uma base comum
 * - Testabilidade: facilita criar mocks e testes unitários
 * - Manutenibilidade: centraliza a lógica de gerenciamento do servidor
 */
export class BaseServer {
  /** Aplicação Express encapsulada. Privado para evitar acesso direto e manter encapsulamento. */
  private app: Application;

  /** Porta em que o servidor irá escutar. */
  private port: number;

  /** Indica se o servidor já foi inicializado. */
  private isInitialized: boolean = false;

  /**
   * Construtor da classe BaseServer.
   *
   * @param port - Porta em que o servidor irá escutar
   */
  constructor(port: number) {
    this.app = express();
    this.port = port;
    this.isInitialized = false;
  }

  /**
   * Aplica middlewares na aplicação Express.
   *
   * Por que método público?
   * - Permite configuração flexível do servidor após a criação
   * - Facilita injeção de dependências (middlewares podem ser passados externamente)
   *
   * @param middlewares - Array de middlewares a serem aplicados
   */
  public useMiddlewares(middlewares: RequestHandler[]): void {
    middlewares.forEach((middleware) => {
      this.app.use(middleware);
    });
  }

  /**
   * Aplica middlewares padrão do Express (JSON e URL encoded).
   *
   * Estes middlewares são comuns na maioria das aplicações REST,
   * então fornecemos um método conveniente para aplicá-los.
   */
  public useDefaultMiddlewares(): void {
    this.app.use(express.json());
    this.app.use(express.urlencoded({ extended: true }));
  }

  /**
   * Registra rotas na aplicação Express.
   *
   * Por que este método é útil?
   * - Encapsula a lógica de registro de rotas
   * - Permite validação e tratamento de erros centralizados
   * - Facilita testes unitários (podemos mockar as rotas)
   *
   * Nota: Este método é síncrono porque o registro de rotas no Express é síncrono.
   * Se você precisar de operações assíncronas durante o registro de rotas,
   * faça-as antes de chamar este método.
   *
   * @param routers - Array de objetos contendo path e router a serem registrados
   */
  public registerRouters(routers: ServRouter[]): void {
    for (const router of routers) {
      if (router && router.path && router.router) {
        this.app.use(router.path, router.router);
      }
    }
  }

  /**
   * Retorna a aplicação Express encapsulada.
   *
   * Útil para casos especiais onde é necessário acesso direto ao app,
   * como testes ou integrações avançadas.
   *
   * @returns A aplicação Express
   */
  public getApp(): Application {
    return this.app;
  }

  /**
   * Retorna a porta configurada do servidor.
   *
   * @returns A porta do servidor
   */
  public getPort(): number {
    return this.port;
  }

  /**
   * Inicializa o servidor e começa a escutar requisições.
   *
   * Por que separar a inicialização da configuração?
   * - Permite configurar o servidor completamente antes de iniciá-lo
   * - Facilita testes (podemos configurar sem iniciar)
   * - Permite validação da configuração antes da inicialização
   * - Torna o código mais previsível e testável
   *
   * @returns Promise que resolve quando o servidor estiver escutando
   */
  public async bootstrap(): Promise<void> {
    if (this.isInitialized) {
      throw new Error("Server has already been initialized");
    }

    return new Promise<void>((resolve) => {
      this.app.listen(this.port, () => {
        this.isInitialized = true;
        console.log(`Server running on port -> ${this.port}`);
        resolve();
      });
    });
  }

  /**
   * Verifica se o servidor já foi inicializado.
   *
   * @returns True se o servidor foi inicializado, false caso contrário
   */
  public isServerInitialized(): boolean {
    return this.isInitialized;
  }
}

/**
 * Factory function que cria e configura uma instância do servidor.
 *
 * PADRÃO FACTORY:
 * O padrão Factory é um padrão de design criacional que fornece uma interface
 * para criar objetos sem especificar exatamente qual classe será instanciada.
 *
 * Por que usar Factory aqui?
 *
 * 1. ENCAPSULAMENTO DA COMPLEXIDADE:
 *    - A factory esconde os detalhes de como o servidor é criado e configurado
 *    - O cliente não precisa saber sobre a ordem de configuração (middlewares antes de rotas, etc.)
 *
 * 2. FLEXIBILIDADE E EXTENSIBILIDADE:
 *    - Permite criar diferentes tipos de servidores (dev, prod, test) com configurações distintas
 *    - Facilita adicionar novas configurações sem quebrar código existente
 *    - Permite criar servidores especializados (API server, Web server, etc.)
 *
 * 3. TESTABILIDADE:
 *    - Facilita criar mocks e stubs para testes
 *    - Permite criar servidores de teste com configurações simplificadas
 *    - Isola a lógica de criação, tornando-a testável independentemente
 *
 * 4. INJEÇÃO DE DEPENDÊNCIAS:
 *    - Permite injetar dependências (middlewares, rotas, configs) de forma controlada
 *    - Facilita o uso de diferentes implementações em diferentes ambientes
 *    - Torna o código mais desacoplado e modular
 *
 * 5. REUTILIZAÇÃO:
 *    - A mesma factory pode ser usada em diferentes partes da aplicação
 *    - Facilita replicar a estrutura em outros projetos
 *    - Centraliza a lógica de criação, evitando duplicação
 *
 * 6. MANUTENIBILIDADE:
 *    - Mudanças na forma como o servidor é criado ficam centralizadas na factory
 *    - Facilita refatorações futuras
 *    - Torna o código mais legível e organizado
 *
 * @param options - Opções de configuração do servidor (porta, middlewares, rotas, etc.)
 * @returns Uma instância configurada do BaseServer, pronta para uso
 *
 * @example
 * ```typescript
 * // Exemplo básico: criar servidor com configurações padrão
 * const server = createServer({ port: 3000 });
 * await server.bootstrap();
 *
 * // Exemplo avançado: criar servidor com middlewares e rotas customizadas
 * const server = createServer({
 *   port: 3000,
 *   useDefaultMiddlewares: true,
 *   middlewares: [cors(), helmet()],
 *   routers: [
 *     { path: '/api', router: apiRouter },
 *     { path: '/auth', router: authRouter }
 *   ]
 * });
 * await server.bootstrap();
 * ```
 */
export function createServer(options: ServerOptions = {}): BaseServer {
  // 1. Determina a porta a ser usada (opção fornecida ou valor do config)
  const port = options.port ?? config.PORT;

  // 2. Cria uma instância do servidor base
  const server = new BaseServer(port);

  // 3. Aplica middlewares padrão se solicitado
  // Por que aplicar aqui? Porque são comuns na maioria das aplicações
  if (options.useDefaultMiddlewares !== false) {
    server.useDefaultMiddlewares();
  }

  // 4. Aplica middlewares customizados se fornecidos
  // Injeção de dependência: os middlewares são injetados externamente
  if (options.middlewares && options.middlewares.length > 0) {
    server.useMiddlewares(options.middlewares);
  }

  // 5. Registra rotas se fornecidas
  // Injeção de dependência: as rotas são injetadas externamente
  if (options.routers && options.routers.length > 0) {
    server.registerRouters(options.routers);
  }

  // 6. Retorna a instância configurada
  // A instância está pronta para uso, mas ainda não foi inicializada
  // O cliente deve chamar bootstrap() quando estiver pronto para iniciar o servidor
  return server;
}

/**
 * Exportação da classe Server como alias para manter compatibilidade com código existente.
 *
 * Nota: Em novos projetos, prefira usar createServer() seguindo o padrão Factory.
 */
export { BaseServer as Server };
````
