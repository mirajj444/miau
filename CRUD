create new database (same name ans in env.)

```bash
php artisan make:model Example -m
```

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Tea extends Model
{
    protected $fillable = ['name', 'type', 'image'];
}
```

or

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;

class Article extends Model
{
    use HasFactory;

    protected $fillable = ['title', 'content', 'user_id'];

    public function user()
    {
        return $this->belongsTo(User::class);
    }
}
```

then in migrations

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('teas', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('type');
            $table->string('image')->nullable();
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('teas');
    }
};
```

```bash
php artisan migrate
```

```bash
php artisan make:controller ExampleController --resource
```

!!! add location model in controller

```php
use App\Models\Tea;
```

example CRUD in controller
```php
<?php

namespace App\Http\Controllers;

use App\Models\Article;
use Illuminate\Http\Request;

class ArticleController extends Controller
{
    /**
     * Display a listing of the resource.
     */
    public function index()
{
    $articles = auth()->user()->articles()->latest()->get();
/* $articles = Article::all(); */
    return view('articles.index', compact('articles'));
}

    /**
     * Show the form for creating a new resource.
     */
    public function create()
{
    return view('articles.create');
}

    /**
     * Store a newly created resource in storage.
     */
    public function store(Request $request)
{
    $request->validate([
        'title' => 'required|string|max:255',
        'content' => 'required|string',
    ]);

    auth()->user()->articles()->create($request->all());

/* Article::create($request->all()); */

    return redirect()->route('articles.index')
                     ->with('success', 'Articolul a fost creat cu succes!');
}

    /**
     * Display the specified resource.
     */
    public function show(Article $article)
{
    // Allow any authenticated user to view any article
    return view('articles.show', compact('article'));
}

public function edit(Article $article)
{
    // Only allow the owner to edit
    if ($article->user_id !== auth()->id()) {
        abort(403, 'Unauthorized action.');
    }

    return view('articles.edit', compact('article')); /* or only this */
}

public function update(Request $request, Article $article)
{
    // Only allow the owner to update
    if ($article->user_id !== auth()->id()) {
        abort(403);
    }

    $request->validate([
        'title' => 'required|string|max:255',
        'content' => 'required|string',
    ]);

    $article->update($request->all());

    return redirect()->route('articles.index')
                     ->with('success', 'Articolul a fost actualizat!');
}

public function destroy(Article $article)
{
    // Only allow the owner to delete
    if ($article->user_id !== auth()->id()) {
        abort(403);
    }

    $article->delete();

    return redirect()->route('articles.index')
                     ->with('success', 'Articolul a fost șters!');
}
}
```

or

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

use App\Models\Tea;

class TeaController extends Controller
{
    /**
     * Display a listing of the resource.
     */
    public function index(Request $request)
    {
        $teas = Tea::when($request->has('search'), function ($query) use ($request) {
            $query->where('name', 'like', '%' . $request->search . '%')
                  ->orWhere('type', 'like', '%' . $request->search . '%');
        })->get();

        return view('teas.index', compact('teas'));
    }

    /**
     * Show the form for creating a new resource.
     */
    public function create()
    {
        return view('teas.create');
    }

    /**
     * Store a newly created resource in storage.
     */
   public function store(Request $request)
{
    $data = $request->validate([
        'name'  => 'required|max:100',
        'type'  => 'required',
        'image' => 'nullable|image',
    ]);

    if ($request->hasFile('image')) {
        $data['image'] = $request->file('image')->store('teas', 'public');
    }

    Tea::create($data);

    return redirect()->route('teas.index')
                     ->with('success', 'Tea created successfully!');
}

    /**
     * Display the specified resource.
     */
    public function show(Tea $tea)
    {
        return view('teas.show', compact('tea'));
    }

    /**
     * Show the form for editing the specified resource.
     */
    public function edit(Tea $tea)
    {
        return view('teas.edit', compact('tea'));
    }

    /**
     * Update the specified resource in storage.
     */
    public function update(Request $request, Tea $tea)
{
    $data = $request->validate([
        'name'  => 'required|max:100',
        'type'  => 'required',
        'image' => 'nullable|image',
    ]);

    if ($request->hasFile('image')) {
        $data['image'] = $request->file('image')->store('teas', 'public');
    }

    $tea->update($data);

    return redirect()->route('teas.index')
                     ->with('success', 'Tea updated successfully!');
}
    /**
     * Remove the specified resource from storage.
     */
    public function destroy(Tea $tea)
    {
        $tea->delete();
        return redirect()->route('teas.index')
                         ->with('success', 'Tea deleted successfully!');
    }

public function manage(Request $request) {
    $teas = Tea::when($request->has('search'), fn($q) =>
        $q->where('name', 'like', '%'.$request->search.'%')
          ->orWhere('type', 'like', '%'.$request->search.'%')
    )->get();
    return view('teas.manage', compact('teas'));
}
}   
```

routes web.php examples

```php
<?php

use Illuminate\Support\Facades\Route;

use App\Http\Controllers\TeaController;

Route::get('/teas/manage', [App\Http\Controllers\TeaController::class, 'manage'])
    ->name('teas.manage');

Route::resource('teas', App\Http\Controllers\TeaController::class);
```

or

```php
<?php

use App\Http\Controllers\ProfileController;
use Illuminate\Support\Facades\Route;
use App\Http\Controllers\ArticleController;
use App\Models\Article;

Route::get('/', function () {
    return view('welcome');
});

Route::get('/dashboard', function () {
    $articles = Article::latest()->get(); // all articles, newest first
    return view('dashboard', compact('articles'));
})->middleware(['auth'])->name('dashboard');

Route::middleware('auth')->group(function () {
    Route::get('/profile', [ProfileController::class, 'edit'])->name('profile.edit');
    Route::patch('/profile', [ProfileController::class, 'update'])->name('profile.update');
    Route::delete('/profile', [ProfileController::class, 'destroy'])->name('profile.destroy');
});

Route::middleware(['auth'])->group(function () {
    Route::resource('articles', ArticleController::class);
});

require __DIR__.'/auth.php';

```

or

```php
<?php

use Illuminate\Support\Facades\Route;

use App\Http\Controllers\AboutController;

use App\Http\Controllers\ArticleController;

Route::resource('articles', ArticleController::class);

```
