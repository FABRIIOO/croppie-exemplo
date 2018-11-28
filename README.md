# croppie-exemplo
<h1>Upload de imagens com croppie; ajax e php</h1>
  <h3>html</h3>
  
     <div id="image_demo" style="width:100%;"></div> 
      <label>
        Escolher Imagem
        <input type="file" name="upload_image" id="upload_image" />
      </label>
      <button class="btn btn-success crop_image" >Proximo</button>

  <h3>JS</h3>
  
    $(document).ready(function(){
    $image_crop = $('#image_demo').croppie({
      enableExif: true,
      viewport: {
        width:288,
        height:288,
        type:'circle' //square
      },
      boundary:{
        width:300,
        height:350
      }
    });

    $('#upload_image').on('change', function(){
      var reader = new FileReader();
      reader.onload = function (event) {
        $image_crop.croppie('bind', {
          url: event.target.result
        }).then(function(){
          console.log('completo.');
        });
      }
      reader.readAsDataURL(this.files[0]);
    });

    $('.crop_image').click(function(event){
      $image_crop.croppie('result', {
        type: 'canvas',
        size: 'viewport'
      }).then(function(response){
        $.ajax({
          url:"http://localhost/REDE_SOCIAL/BACK-END/upload.php",
          type: "POST",
          data:{"image": response},
          success:function(data)
           {
             console.log(data);
           }
          });
        })
      });
    });  
  
  <h3>PHP</h3>
  
  	  $data = $_POST["image"];
			$image_array_1 = explode(";", $data);    	
			$image_array_2 = explode(",", $image_array_1[1]);
			$data = base64_decode($image_array_2[1]);
			$imageName = 'imagem/'.time() . '.png';
			file_put_contents($imageName, $data);
			echo '{"result": true,"img":"'.$imageName.'"}';
