<script>
    import "../app.css";
    import Sidebar from "../components/sidebar.svelte";
    import Navbar from "../components/navbar.svelte";
</script>
<div class = "bg-gradient-to-r from-slate-200 to-slate-100 w-full max-h-screen overflow-y-auto">
<div class = " flex relative w-screen">
  <div class = "min-h-screen">
    <Sidebar/>  
  </div>
  <div class = "align-top w-full max-h-screen overflow-y-auto">
    <Navbar/>

  <slot></slot>
  </div>
  
</div>
</div>

