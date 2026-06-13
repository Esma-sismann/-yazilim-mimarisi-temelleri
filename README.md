# 🏗️ Yazılım Mimarisi Temelleri

> Monolith vs Microservice · MVC & MVVM · State Management · Katmanlı Mimari

![Architecture](https://img.shields.io/badge/Software-Architecture-blue?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Tamamlandı-green?style=for-the-badge)
![Language](https://img.shields.io/badge/Dil-Türkçe-red?style=for-the-badge)

---

## 📋 İçindekiler

1. [Monolith vs Microservice](#1-monolith-vs-microservice)
2. [Mimari Desenler: MVC & MVVM](#2-mimari-desenler-mvc--mvvm)
3. [State Management Nedir?](#3-state-management-nedir)
4. [Katmanlı Mimari](#4-katmanlı-mimari-service--repository--controller)
5. [Hangi Mimariyi Seçmeli?](#5-hangi-mimariyi-seçmeli)
6. [Özet Karşılaştırma Tablosu](#6-özet-karşılaştırma-tablosu)

---

## 1. Monolith vs Microservice

### 🧱 Monolithic Mimari

Monolithic (Monolitik) mimari, tüm uygulama bileşenlerinin **tek bir kod tabanı ve tek bir deploy birimi** olarak çalıştığı geleneksel yaklaşımdır.

```
┌──────────────────────────────────────────────┐
│               MONOLITH UYGULAMA              │
│                                              │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  │
│  │    UI    │  │ Business │  │   Data   │  │
│  │  Layer   │  │  Logic   │  │  Access  │  │
│  └──────────┘  └──────────┘  └──────────┘  │
│                                              │
└──────────────────┬───────────────────────────┘
                   │
            ┌──────▼──────┐
            │   Database  │
            └─────────────┘
```

**✅ Avantajları:**
- Geliştirmesi ve debug etmesi kolaydır
- Deployment basittir (tek bir uygulama deploy edilir)
- Servisler arası iletişim maliyeti yoktur (in-process çağrılar)
- Küçük ekipler ve erken aşama projeler için idealdir
- Test yazımı daha kolaydır

**❌ Dezavantajları:**
- Uygulama büyüdükçe karmaşıklık artar
- Tek bir modülün hatası tüm sistemi etkileyebilir
- Bağımsız ölçeklendirme (scaling) yapılamaz
- Farklı teknoloji yığınları (tech stack) kullanmak zorlaşır
- Büyük ekiplerde merge çakışmaları sık yaşanır

---

### 🔬 Microservice Mimari

Microservice mimaride uygulama, her biri **bağımsız olarak geliştirilebilen, deploy edilebilen ve ölçeklendirilebilen** küçük servislere bölünür.

```
┌────────────┐    ┌────────────┐    ┌────────────┐
│   User     │    │   Order    │    │  Payment   │
│  Service   │    │  Service   │    │  Service   │
│            │    │            │    │            │
│ ┌────────┐ │    │ ┌────────┐ │    │ ┌────────┐ │
│ │  DB    │ │    │ │  DB    │ │    │ │  DB    │ │
│ └────────┘ │    │ └────────┘ │    │ └────────┘ │
└─────┬──────┘    └─────┬──────┘    └─────┬──────┘
      │                 │                 │
      └─────────────────┼─────────────────┘
                        │
               ┌────────▼────────┐
               │   API Gateway   │
               └─────────────────┘
                        │
               ┌────────▼────────┐
               │     Client      │
               └─────────────────┘
```

**✅ Avantajları:**
- Her servis bağımsız olarak ölçeklendirilebilir
- Farklı servisler farklı teknoloji yığınlarıyla yazılabilir
- Bir servisin çökmesi diğerlerini doğrudan etkilemez
- Küçük, odaklanmış ekipler bağımsız çalışabilir
- Sürekli deployment (CI/CD) kolaylaşır

**❌ Dezavantajları:**
- Dağıtık sistem yönetimi karmaşıktır
- Servisler arası ağ iletişimi gecikme (latency) yaratır
- Distributed tracing ve logging zordur
- Veri tutarlılığı (consistency) sağlamak karmaşıklaşır
- DevOps olgunluğu gerektirir (Kubernetes, Docker vb.)

---

### 🆚 Monolith vs Microservice Karşılaştırması

| Özellik | Monolith | Microservice |
|---|---|---|
| **Deployment** | Tek unit | Bağımsız servisler |
| **Ölçeklendirme** | Tüm uygulama | Servis bazında |
| **Teknoloji** | Tek stack | Farklı stack'ler |
| **Geliştirme Hızı** | Başlangıçta hızlı | Başlangıçta yavaş |
| **Bakım** | Büyüyünce zorlaşır | İyi yönetilirse kolay |
| **Hata İzolasyonu** | Zor | Kolay |
| **Takım Boyutu** | Küçük ekip | Büyük ekip |

> 💡 **Tavsiye:** Projenizin erken aşamalarında Monolith ile başlayın. Sistem büyüdükçe ve ihtiyaç doğdukça Microservice'e geçiş yapın. Bu yaklaşım "Modular Monolith → Microservice" olarak bilinir.

---

## 2. Mimari Desenler: MVC & MVVM

Mimari desenler (architectural patterns), kodun **nasıl organize edileceğini** belirleyen şablonlardır. En yaygın ikisi MVC ve MVVM'dir.

---

### 🎯 MVC — Model · View · Controller

MVC, uygulamayı üç katmana ayırır:

```
┌─────────────────────────────────────────────────────┐
│                    MVC AKIŞI                        │
│                                                     │
│   User Input                                        │
│       │                                             │
│       ▼                                             │
│  ┌──────────┐    günceller    ┌──────────┐         │
│  │CONTROLLER│ ──────────────► │  MODEL   │         │
│  │          │                 │          │         │
│  │ İstekleri│                 │ Veri ve  │         │
│  │ karşılar │                 │ İş Mantığı         │
│  └──────────┘                 └────┬─────┘         │
│       │                            │               │
│       │                     bildirir               │
│       ▼                            ▼               │
│  ┌──────────┐    render eder  ┌──────────┐         │
│  │   VIEW   │ ◄────────────── │          │         │
│  │          │                 │          │         │
│  │ Kullanıcı│                 │          │         │
│  │ Arayüzü  │                 │          │         │
│  └──────────┘                 └──────────┘         │
└─────────────────────────────────────────────────────┘
```

| Bileşen | Sorumluluk | Örnek |
|---|---|---|
| **Model** | Veri ve iş mantığı | `UserModel`, `ProductModel` |
| **View** | Kullanıcı arayüzü | HTML template, JSX |
| **Controller** | İstekleri karşılar, M-V arası koordinasyon | `UserController` |

**Kullanım Alanları:** Ruby on Rails, Laravel, ASP.NET MVC, Spring MVC




class UserModel {
  static async findById(id) {
    return await db.query('SELECT * FROM users WHERE id = ?', [id]);
  }
}


class UserController {
  async getUser(req, res) {
    const user = await UserModel.findById(req.params.id);
    res.render('user-profile', { user }); // VIEW'e geçir
  }
}


---

### 📱 MVVM — Model · View · ViewModel

MVVM, özellikle **reaktif UI framework'leri** için tasarlanmıştır. Controller yerine ViewModel kullanır ve iki yönlü veri bağlama (two-way data binding) sağlar.

```
┌─────────────────────────────────────────────────────┐
│                   MVVM AKIŞI                        │
│                                                     │
│  ┌──────────┐   Two-Way    ┌───────────┐           │
│  │          │◄────Binding──►│           │           │
│  │   VIEW   │              │ VIEWMODEL │           │
│  │          │              │           │           │
│  │  Sadece  │              │ UI mantığı│           │
│  │  Görsel  │              │ Commands  │           │
│  └──────────┘              └─────┬─────┘           │
│                                  │                 │
│                           veri çeker               │
│                                  │                 │
│                            ┌─────▼─────┐           │
│                            │   MODEL   │           │
│                            │           │           │
│                            │ Veri ve   │           │
│                            │ İş Mantığı│           │
│                            └───────────┘           │
└─────────────────────────────────────────────────────┘
```

| Bileşen | Sorumluluk |
|---|---|
| **Model** | Ham veri ve iş kuralları |
| **View** | Yalnızca görsel; mantık içermez |
| **ViewModel** | View için hazırlanan veri, komutlar ve state |

**Kullanım Alanları:** Vue.js, Angular, React (hooks ile), Flutter, WPF, SwiftUI

const userService = {
  async fetchUser(id) {
    return await api.get(`/users/${id}`);
  }
};

const useUserViewModel = (userId) => {
  const user = ref(null);       // reaktif state
  const isLoading = ref(false);
  const error = ref(null);

  const loadUser = async () => {
    isLoading.value = true;
    try {
      user.value = await userService.fetchUser(userId);
    } catch (e) {
      error.value = e.message;
    } finally {
      isLoading.value = false;
    }
  };

  return { user, isLoading, error, loadUser };
};


```

---

### 🆚 MVC vs MVVM

| Özellik | MVC | MVVM |
|---|---|---|
| **Veri Akışı** | Tek yönlü | İki yönlü (binding) |
| **Test Edilebilirlik** | Orta | Yüksek (ViewModel test edilebilir) |
| **UI Bağımlılığı** | Controller UI bilir | ViewModel UI bilmez |
| **Karmaşıklık** | Düşük-Orta | Orta-Yüksek |
| **Kullanım Alanı** | Web backend | Modern frontend & mobil |

---

## 3. State Management Nedir?

### 📦 Temel Kavram

**State (durum)**, uygulamanın herhangi bir anındaki verilerinin bütününüdür. State management ise bu verinin **nasıl saklandığını, güncellendiğini ve bileşenler arasında nasıl paylaşıldığını** yönetir.

```
Kullanıcı giriş yaptı mı?     → Authentication State
Sepette kaç ürün var?          → Cart State
Hangi tema seçili? (dark/light) → UI State
API'den veri yükleniyor mu?    → Loading State
```

---

### 🔄 State Management Sorunu

```
Bileşen A ────── state değişti ──────► Bileşen B
    │                                       │
    │                                       │
    ▼                                       ▼
Bileşen C                             Bileşen D
(haberi yok!)                         (haberi yok!)
```

Uygulama büyüdükçe state'i bileşenler arasında yönetmek zorlaşır. Bu sorunu çözmek için **merkezi state yönetimi** kullanılır:

```
              ┌──────────────┐
              │  MERKEZI     │
              │  STORE       │
              │  (State)     │
              └──────┬───────┘
         ┌────────────┼────────────┐
         ▼            ▼            ▼
    Bileşen A    Bileşen B    Bileşen C
    (subscribe)  (subscribe)  (subscribe)
```

---

### 🛠️ Popüler State Management Kütüphaneleri

#### Redux (React)


const incrementCounter = () => ({ type: 'INCREMENT' });


const counterReducer = (state = { count: 0 }, action) => {
  switch (action.type) {
    case 'INCREMENT':
      return { count: state.count + 1 };
    default:
      return state;
  }
};


const store = createStore(counterReducer);
store.dispatch(incrementCounter());
console.log(store.getState()); // { count: 1 }
```

#### Pinia (Vue.js)

export const useCartStore = defineStore('cart', {
  state: () => ({
    items: [],
    total: 0
  }),
  actions: {
    addItem(product) {
      this.items.push(product);
      this.total += product.price;
    }
  },
  getters: {
    itemCount: (state) => state.items.length
  }
});
```

#### BLoC (Flutter)
```dart
// Event
abstract class CounterEvent {}
class IncrementEvent extends CounterEvent {}

// BLoC
class CounterBloc extends Bloc<CounterEvent, int> {
  CounterBloc() : super(0) {
    on<IncrementEvent>((event, emit) => emit(state + 1));
  }
}
```

---

### 📊 State Türleri

| Tür | Açıklama | Örnek |
|---|---|---|
| **Local State** | Tek bileşene ait | Modal açık/kapalı |
| **Global State** | Tüm uygulama geneli | Oturum bilgisi |
| **Server State** | API'den gelen veri | Ürün listesi |
| **URL State** | Adres çubuğu | Arama filtresi |
| **Form State** | Form verileri | Kayıt formu |

> 💡 **Altın Kural:** Her state'i mümkün olduğunca yerel tutun. Gerçekten paylaşılması gerekiyorsa global store kullanın.

---

## 4. Katmanlı Mimari: Service · Repository · Controller

### 🏛️ Separation of Concerns (Sorumlulukların Ayrılması)

Katmanlı mimari, uygulamanın her katmanının **tek bir sorumluluğu** olduğu, temiz ve bakımı kolay bir yapı sunar.

```
┌─────────────────────────────────────────────┐
│                   CLIENT                   │
│            (Browser / Mobile App)           │
└───────────────────┬─────────────────────────┘
                    │ HTTP Request
                    ▼
┌─────────────────────────────────────────────┐
│              CONTROLLER LAYER               │
│   • HTTP isteklerini karşılar               │
│   • Input validasyon                        │
│   • Response formatlar                      │
│   • Routing                                 │
└───────────────────┬─────────────────────────┘
                    │ Servis çağrısı
                    ▼
┌─────────────────────────────────────────────┐
│               SERVICE LAYER                 │
│   • İş mantığı (business logic)             │
│   • Kurallar ve hesaplamalar                │
│   • Birden fazla repository koordinasyonu   │
│   • Transaction yönetimi                    │
└───────────────────┬─────────────────────────┘
                    │ Veri erişimi
                    ▼
┌─────────────────────────────────────────────┐
│             REPOSITORY LAYER                │
│   • Veritabanı işlemleri (CRUD)             │
│   • Sorgu oluşturma                         │
│   • ORM/Query Builder kullanımı             │
│   • Cache yönetimi                          │
└───────────────────┬─────────────────────────┘
                    │ SQL / NoSQL
                    ▼
┌─────────────────────────────────────────────┐
│                 DATABASE                    │
│         (PostgreSQL / MongoDB vb.)          │
└─────────────────────────────────────────────┘
```

---

### 💻 Kod Örneği: Kullanıcı Sistemi (Node.js + Express)

#### 📁 Proje Yapısı
```
src/
├── controllers/
│   └── userController.js
├── services/
│   └── userService.js
├── repositories/
│   └── userRepository.js
├── models/
│   └── User.js
└── routes/
    └── userRoutes.js
```

#### 🔵 Controller Katmanı

class UserController {
  constructor(userService) {
    this.userService = userService;
  }

 
  async register(req, res) {
    try {
      const { name, email, password } = req.body;

      
      if (!email || !password) {
        return res.status(400).json({ error: 'Email ve şifre zorunludur' });
      }

      const user = await this.userService.registerUser({ name, email, password });

      return res.status(201).json({
        message: 'Kullanıcı oluşturuldu',
        data: { id: user.id, name: user.name, email: user.email }
      });
    } catch (error) {
      return res.status(400).json({ error: error.message });
    }
  }

  async getProfile(req, res) {
    try {
      const user = await this.userService.getUserById(req.params.id);
      return res.json({ data: user });
    } catch (error) {
      return res.status(404).json({ error: error.message });
    }
  }
}

module.exports = UserController;
```

#### 🟡 Service Katmanı
```javascript
// services/userService.js
const bcrypt = require('bcrypt');

class UserService {
  constructor(userRepository) {
    this.userRepository = userRepository;
  }

  // İş mantığı burada yaşar
  async registerUser({ name, email, password }) {
    // İş kuralı: Email zaten kayıtlı mı?
    const existingUser = await this.userRepository.findByEmail(email);
    if (existingUser) {
      throw new Error('Bu email adresi zaten kullanılıyor');
    }

    // İş mantığı: Şifreyi hash'le
    const hashedPassword = await bcrypt.hash(password, 10);

    // İş mantığı: Varsayılan değerleri belirle
    const userData = {
      name,
      email,
      password: hashedPassword,
      role: 'user',
      createdAt: new Date()
    };

    return await this.userRepository.create(userData);
  }

  async getUserById(id) {
    const user = await this.userRepository.findById(id);
    if (!user) {
      throw new Error('Kullanıcı bulunamadı');
    }

    // İş mantığı: Hassas alanları gizle
    const { password, ...safeUser } = user;
    return safeUser;
  }

  async updateUserEmail(userId, newEmail) {
    // İş kuralı: Yeni email başkası tarafından kullanılıyor mu?
    const conflict = await this.userRepository.findByEmail(newEmail);
    if (conflict && conflict.id !== userId) {
      throw new Error('Bu email adresi başkası tarafından kullanılıyor');
    }

    return await this.userRepository.update(userId, { email: newEmail });
  }
}

module.exports = UserService;
```

#### 🟢 Repository Katmanı
```javascript
// repositories/userRepository.js

class UserRepository {
  constructor(db) {
    this.db = db; // Database bağlantısı (Knex, Prisma, TypeORM vb.)
  }

  // Sadece veritabanı işlemleri — iş mantığı YOK
  async findById(id) {
    return await this.db('users').where({ id }).first();
  }

  async findByEmail(email) {
    return await this.db('users').where({ email }).first();
  }

  async create(userData) {
    const [id] = await this.db('users').insert(userData);
    return this.findById(id);
  }

  async update(id, data) {
    await this.db('users').where({ id }).update(data);
    return this.findById(id);
  }

  async delete(id) {
    return await this.db('users').where({ id }).del();
  }

  async findAll({ page = 1, limit = 10 } = {}) {
    const offset = (page - 1) * limit;
    return await this.db('users')
      .select('id', 'name', 'email', 'createdAt')
      .limit(limit)
      .offset(offset);
  }
}

module.exports = UserRepository;
```

#### 🔗 Bağımlılıkları Birleştirme (Dependency Injection)


const db = require('./database');
const UserRepository = require('./repositories/userRepository');
const UserService = require('./services/userService');
const UserController = require('./controllers/userController');


const userRepository = new UserRepository(db);
const userService = new UserService(userRepository);
const userController = new UserController(userService);


app.post('/api/users/register', (req, res) => userController.register(req, res));
app.get('/api/users/:id', (req, res) => userController.getProfile(req, res));
```

---

### 🎯 Her Katmanın Sorumluluk Sınırları

```
❌ YANLIŞ: Controller içinde veritabanı sorgusu
   → controller doğrudan db.query() çağırmamalı

❌ YANLIŞ: Repository içinde iş kuralı
   → repository "şifre zaten kullanılıyor" kontrolü yapmamalı

❌ YANLIŞ: Service içinde HTTP response
   → service res.json() çağırmamalı

✅ DOĞRU: Her katman yalnızca kendi işini yapar
   → Controller: HTTP ↔ Service
   → Service: İş mantığı ↔ Repository
   → Repository: Uygulama ↔ Veritabanı
```

---

### 🧪 Test Edilebilirlik Avantajı

Katmanlı mimari, her katmanı **bağımsız olarak test etmeye** imkân tanır:


describe('UserService', () => {
  it('zaten kayıtlı email ile kayıt olmayı reddetmeli', async () => {
    // Repository'yi mock'luyoruz — gerçek DB gerekmez
    const mockRepository = {
      findByEmail: jest.fn().mockResolvedValue({ id: 1, email: 'test@test.com' }),
      create: jest.fn()
    };

    const service = new UserService(mockRepository);

    await expect(
      service.registerUser({ name: 'Ali', email: 'test@test.com', password: '123456' })
    ).rejects.toThrow('Bu email adresi zaten kullanılıyor');

    expect(mockRepository.create).not.toHaveBeenCalled();
  });
});
```

---

## 5. Hangi Mimariyi Seçmeli?

### 🗺️ Karar Rehberi

```
Projeniz ne aşamada?
│
├── Yeni başlıyorum / MVP
│   └── → Monolith + Katmanlı Mimari + MVC
│
├── Orta ölçekli, büyüyen ekip
│   └── → Modular Monolith + MVVM (frontend) + Service Layer
│
└── Büyük ölçekli, çok ekip
    └── → Microservice + API Gateway + State Management
```

### 📌 Framework Eşleştirmeleri

| Platform | Mimari Desen | State Management | Mimari Yaklaşım |
|---|---|---|---|
| React | MVVM / Flux | Redux, Zustand, Jotai | Monolith veya Micro-frontend |
| Vue.js | MVVM | Pinia, Vuex | Monolith veya Micro-frontend |
| Angular | MVC + MVVM | NgRx, Signals | Monolith |
| Flutter | MVVM / BLoC | BLoC, Riverpod, Provider | Monolith |
| Laravel | MVC | — | Monolith veya API backend |
| Spring Boot | MVC + Katmanlı | — | Monolith veya Microservice |
| Express.js | Katmanlı | — | Monolith veya Microservice |

---

## 6. Özet Karşılaştırma Tablosu

| Kavram | Ne Sorar? | Çözüm |
|---|---|---|
| **Monolith vs Microservice** | Uygulama nasıl dağıtılacak? | Küçükse Monolith, büyükse Micro |
| **MVC** | Kod nasıl organize edilecek? | Model-View-Controller ayrımı |
| **MVVM** | UI ile mantık nasıl ayrılacak? | Two-way binding + ViewModel |
| **State Management** | Uygulama durumu nasıl yönetilecek? | Merkezi Store (Redux, Pinia vb.) |
| **Katmanlı Mimari** | İş mantığı nereye yazılacak? | Controller → Service → Repository |

---

## 📚 Kaynaklar ve İleri Okuma

- [Martin Fowler - Patterns of Enterprise Application Architecture](https://martinfowler.com/books/eaa.html)
- [Microsoft - Microservices Architecture](https://docs.microsoft.com/en-us/azure/architecture/guide/architecture-styles/microservices)
- [Redux Documentation](https://redux.js.org/)
- [Clean Architecture - Robert C. Martin](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [Vue.js - Pinia State Management](https://pinia.vuejs.org/)

---

<div align="center">

**⭐ Faydalı bulduysan yıldız vermeyi unutma!**



</div>
