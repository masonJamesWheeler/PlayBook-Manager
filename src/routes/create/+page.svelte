<script>
import {onMount} from 'svelte'
import Datasheet from '../../components/datasheet.svelte'

// define webcomponent element in DOM


</script>


<div class = "bg-gradient-to-r from-slate-200 to-slate-100 w-full">
    <div class = "text-3xl font-extrabold mx-24 bg-clip-text text-transparent bg-gradient-to-r from-violet-600 to-orange-500 flex">
    <div class = "mt-24 mb-12"> 
    <h3> The Best Way To Create Installs, Scripts, and Scout Cards</h3> 
        <h3>Integrated Seamlessly With Your Athletes.</h3>
    </div>  
   </div>
   <div>
   <div class="grid grid-rows-2 grid-flow-col gap-4 mx-20 mb-10">
    <div class="row-span-3 ..."><div class="card w-96 bg-base-100 shadow-xl">
        <figure class="px-6 pt-4">
          <img src = "proto.png" alt="Shoes" class="rounded-xl" />
        </figure>
        <div class="card-body items-center text-center">
          <p class="card-title font-sans font-semibold">Have a play to load into PlayBook Manager?</p>
          <div class="card-actions">
            <a class="btn btn-primary" href="/post">Click Here</a>
          </div>
        </div>
      </div></div>
    <div class="col-span-2 ...">
        <div class="card card-side bg-base-100 shadow-xl">
            <figure><img src="https://placeimg.com/200/280/arch"/></figure>
            <div class="card-body">
              <h2 class="card-title">Create Practice Scripts Automatically!</h2>
              <div class="card-actions justify-end">
                <button class="btn btn-primary">Watch</button>
              </div>
            </div>
          </div>
    </div>
    <div class="row-span-2 col-span-2 ..."><div class = "w-full">
       <Datasheet/>
    </div>
</div>
  </div>
</div>



</div>