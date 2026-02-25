<template>
  <header>
    <a href="#" class="logo">
      <img src="Images/logotr.png" width="190px" alt="">
    </a>
    <input type="checkbox" id="check">
    <label for="check" class="icons">
      <i class="fa-solid fa-bars" id="menu-icon"></i>
      <i class="fa-solid fa-xmark" id="close-icon"></i>
    </label>
    <nav class="navigation">
  <router-link to="/" class="nav-links" style="--i:0;" @click="closeMenu">Home</router-link>
  <div class="dropdown" style="--i:1;"
    @click="showDropdown = !showDropdown"
  >
    <router-link to="/packages" class="nav-links" style="--i:2;" @click.native.stop>Services</router-link>
    <ul class="dropdown-menu" v-show="showDropdown">
      <li><router-link to="/interior" class="dropdown-item">Interior</router-link></li>
      <li><router-link to="/dry-ice" class="dropdown-item">Dry Ice</router-link></li>
      <li><router-link to="/ppf" class="dropdown-item">PPF</router-link></li>
      <li><router-link to="/executive" class="dropdown-item">Executive</router-link></li>
      <li><router-link to="/presidential" class="dropdown-item">Presidential</router-link></li>
      <li><router-link to="/signature" class="dropdown-item">Signature</router-link></li>
    </ul>
  </div>
  <router-link to="/about" class="nav-links" style="--i:3;">About Us</router-link>
  <router-link to="/contact" class="nav-links" style="--i:4;">Contact Us</router-link>
      <template v-if="isLoggedIn">
        <button @click="logout" class="nav-links auth-action">Logout</button>
        <span class="nav-links user-welcome">Welcome, {{ userName }}</span>
      </template>
      <template v-else>
        <router-link to="/login" class="nav-links auth-action">Login</router-link>
      </template>
    </nav>
  </header>
</template>

<script>
export default {
  name: 'AppNavbar',
  data() {
    return {
      isLoggedIn: !!localStorage.getItem('user'),
      userName: this.getUserName(),
  showDropdown: false
    }
  },
  mounted() {
    window.addEventListener('storage', this.syncLoginState);
    window.addEventListener('user-logged-in', this.syncLoginState);
    if (this.$router) {
      this.unwatchRoute = this.$router.afterEach(() => {
        this.syncLoginState();
      });
    }
  },
  beforeUnmount() {
    window.removeEventListener('storage', this.syncLoginState);
    window.removeEventListener('user-logged-in', this.syncLoginState);
    if (this.unwatchRoute) {
      this.unwatchRoute();
    }
    if (this.dropdownTimeout) {
      clearTimeout(this.dropdownTimeout);
    }
  },
  methods: {
    getUserName() {
      const userStr = localStorage.getItem('user');
      if (!userStr) return '';
      try {
        const user = JSON.parse(userStr);
        return user.name || user.firstName || user.username || user.email || '';
      } catch {
        return '';
      }
    },
    async logout() {
      if (window.confirm('Are you sure you want to log out of your Auto Stallion account? You will need to log in again to access your account.')) {
        try {
          await fetch('http://localhost:5000/api/auth/logout', {
            method: 'POST',
            credentials: 'include',
            headers: {
              'Content-Type': 'application/json'
            }
          });
        } catch (err) {
        }
        localStorage.removeItem('user');
        localStorage.removeItem('role');
        this.isLoggedIn = false;
        this.$router.push('/');
      }
    },
    syncLoginState() {
      this.isLoggedIn = !!localStorage.getItem('user');
      this.userName = this.getUserName();
    },
    closeMenu() {
  document.getElementById("check").checked = false;
}
  },
}
</script>

<style scoped>
/* =========================
   HEADER BASE
========================= */

header {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  padding: 1.2rem 2rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
  z-index: 100;
  background: rgba(0, 0, 0, 0.6);
  backdrop-filter: blur(20px);
  border-bottom: 1px solid #232323;
}

.logo img {
  max-width: 170px;
  width: 100%;
  height: auto;
}

/* =========================
   NAVIGATION DESKTOP
========================= */

.navigation {
  display: flex;
  align-items: center;
  gap: 1.5rem;
}

.navigation a,
.navigation button {
  font-size: 1rem;
  color: #fff;
  text-decoration: none;
  font-weight: 600;
  background: none;
  border: none;
  cursor: pointer;
  position: relative;
  transition: color 0.2s ease;
}

.navigation a:hover,
.navigation button:hover {
  color: #c1902d;
}

.navigation .router-link-active,
.navigation .router-link-exact-active {
  color: #c1902d !important;
}

/* Underline animation */
.nav-links::after {
  content: "";
  position: absolute;
  left: 0;
  bottom: -6px;
  width: 0;
  height: 2px;
  background: #c1902d;
  transition: width 0.3s ease;
}

.nav-links:hover::after {
  width: 100%;
}

/* =========================
   DROPDOWN DESKTOP
========================= */

.dropdown {
  position: relative;
}

.dropdown-menu {
  position: absolute;
  top: 110%;
  left: 0;
  width: 220px;
  background: #1c1c1c;
  border-radius: 10px;
  padding: 0.5rem 0;
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.4);
  border: 1px solid #333;
  z-index: 200;
}

.dropdown-menu li {
  list-style: none;
}

.dropdown-item {
  display: block;
  padding: 0.7rem 1.2rem;
  color: #fff;
  text-decoration: none;
  font-size: 0.95rem;
  transition: background 0.2s ease;
}

.dropdown-item:hover {
  background: #2a2a2a;
  color: #c1902d;
}

/* =========================
   MOBILE MENU ICON
========================= */

#check {
  display: none;
}

.icons {
  display: none;
  font-size: 1.8rem;
  cursor: pointer;
  color: #fff;
}

/* Hide close icon by default */
#close-icon {
  display: none;
}

/* When menu is open */
#check:checked ~ .icons #menu-icon {
  display: none;
}

#check:checked ~ .icons #close-icon {
  display: inline-block;
}

@media (max-width: 900px) {

  header {
    padding: 1rem 1.5rem;
  }

  .icons {
    display: block;
    position: absolute;
    right: 1.5rem;
    z-index: 101;
  }

  .navigation {
    position: absolute;
    top: 100%;
    left: 0;
    width: 100%;
    background: #000;
    flex-direction: column;
    align-items: center;
    gap: 0;
    overflow: hidden;
    max-height: 0;
    transition: max-height 0.4s ease;
  }

  #check:checked ~ .navigation {
    max-height: 700px;
    padding: 1rem 0;
  }

  .navigation a,
  .navigation button {
    width: 100%;
    text-align: center;
    padding: 1rem 0;
    margin: 0;
    font-size: 1rem;
  }

  /* Make dropdown expand properly */
  .dropdown {
    width: 100%;
    text-align: center;
  }

  .dropdown-menu {
    position: relative;
    width: 100%;
    background: #111;
    box-shadow: none;
    border: none;
  }

  .dropdown-item {
    text-align: center;
    padding: 0.8rem 0;
  }

  .user-welcome {
    display: none;
  }
}

</style>
