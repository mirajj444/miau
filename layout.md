layout

```php
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="csrf-token" content="{{ csrf_token() }}">
    <title>{{ config('app.name', 'Laravel') }}</title>

    @vite(['resources/css/app.css', 'resources/js/app.js'])

    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
</head>
<body>
    <nav class="navbar">
        <div class="navbar-container">
            <div class="navbar-logo">
                <a href="{{ route('dashboard') }}">
                    <i class="fa-regular fa-folder-open"></i>
                </a>
            </div>

            <div class="nav-links">
                <x-nav-link :href="route('dashboard')" :active="request()->routeIs('dashboard')" class="nav-link">
                    {{ __('Dashboard') }}
                </x-nav-link>

                <x-nav-link :href="route('articles.index')" :active="request()->routeIs('articles.*')" class="nav-link">
                    {{ __('My Articles') }}
                </x-nav-link>
            </div>

            <div class="user-dropdown">
                <x-dropdown align="right" width="48">
                    <x-slot name="trigger">
                        <button class="dropdown-trigger">
                            <span>Welcome, {{ Auth::user()->name }}!</span>
                            <svg class="dropdown-icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20">
                                <path fill-rule="evenodd" d="M5.293 7.293a1 1 0 011.414 0L10 10.586l3.293-3.293a1 1 0 111.414 1.414l-4 4a1 1 0 01-1.414 0l-4-4a1 1 0 010-1.414z" clip-rule="evenodd" />
                            </svg>
                        </button>
                    </x-slot>
                    <div class="dropdown-content">
                        <x-slot name="content">
                            <x-dropdown-link :href="route('profile.edit')" class="dropdown-link">
                                <i class="fa-regular fa-user"></i> {{ __('Profile') }}
                            </x-dropdown-link>

                            <form method="POST" action="{{ route('logout') }}">
                                @csrf
                                <x-dropdown-link :href="route('logout')"
                                        onclick="event.preventDefault(); this.closest('form').submit();"
                                        class="dropdown-link">
                                    <i class="fa-solid fa-right-from-bracket"></i> {{ __('Log Out') }}
                                </x-dropdown-link>
                            </form>
                        </x-slot>
                    </div>
                </x-dropdown>
            </div>
        </div>
    </nav>

    <main class="main-content">
        @yield('content')
    </main>
</body>
</html>
```

index

```php
@extends('layouts.app')

@section('content')
    <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 1.5rem;">
        <h2 style="font-size: 1.875rem; font-weight: 700;">My Articles</h2>
        <a href="{{ route('articles.create') }}" class="btn-primary" style="text-decoration: none;"><i class="fa-solid fa-plus"></i> New Article</a>
    </div>

    @if(session('success'))
        <div class="alert-success">
            {{ session('success') }}
        </div>
    @endif

    @if($articles->count())
        <table class="articles-table">
            <thead>
                <tr>
                    <th>Title</th>
                    <th>Description</th>
                    <th>Actions</th>
                </tr>
            </thead>
            <tbody>
                @foreach($articles as $article)
                    <tr>
                        <td>{{ $article->title }}</td>
                        <td>{{ Str::limit($article->content, 120) }}</td>
                        <td class="actions-cell">
                            <a href="{{ route('articles.show', $article) }}" class="action-btn view-btn" title="View">
                                <i class="fas fa-eye"></i>
                            </a>

                            @if($article->user_id === auth()->id())
                                <a href="{{ route('articles.edit', $article) }}" class="action-btn edit-btn" title="Edit">
                                    <i class="fas fa-edit"></i>
                                </a>

                                <form method="POST" action="{{ route('articles.destroy', $article) }}" style="display:inline;" onsubmit="return confirm('Are you sure?')">
                                    @csrf
                                    @method('DELETE')
                                    <button type="submit" class="action-btn delete-btn" title="Delete">
                                        <i class="fas fa-trash-alt"></i>
                                    </button>
                                </form>
                            @endif
                        </td>
                    </tr>
                @endforeach
            </tbody>
        </table>
    @else
        <p style="text-align: center; color: #6b7280; margin-top: 3rem;">
            You don't have any articles yet. <a href="{{ route('articles.create') }}" style="color: #ec1668;">Create your first article</a>!
        </p>
    @endif
@endsection
```

create

```php
@extends('layouts.app')

@section('content')
    <div class="form-container">
        <h2 style="font-size: 1.5rem; font-weight: 700; margin-bottom: 1.5rem;">Create a New Article</h2>

        <form method="POST" action="{{ route('articles.store') }}">
            @csrf

            <div class="form-group">
                <label for="title" class="form-label">Title</label>
                <input type="text" name="title" id="title" value="{{ old('title') }}" class="form-control @error('title') is-invalid @enderror">
                @error('title')
                    <p class="form-error">{{ $message }}</p>
                @enderror
            </div>

            <div class="form-group">
                <label for="content" class="form-label">Content</label>
                <textarea name="content" id="content" rows="6" class="form-control @error('content') is-invalid @enderror">{{ old('content') }}</textarea>
                @error('content')
                    <p class="form-error">{{ $message }}</p>
                @enderror
            </div>

            <div class="form-actions">
                <button type="submit" class="btn-primary">Save</button>
                <a href="{{ route('articles.index') }}" class="btn-link">Cancel</a>
            </div>
        </form>
    </div>
@endsection
```

edit

```php
@extends('layouts.app')

@section('content')
    <div class="form-container">
        <h2 style="font-size: 1.5rem; font-weight: 700; margin-bottom: 1.5rem;">Edit Article</h2>

        <form method="POST" action="{{ route('articles.update', $article) }}">
            @csrf
            @method('PUT')

            <div class="form-group">
                <label for="title" class="form-label">Title</label>
                <input type="text" name="title" id="title" value="{{ old('title', $article->title) }}" class="form-control @error('title') is-invalid @enderror">
                @error('title')
                    <p class="form-error">{{ $message }}</p>
                @enderror
            </div>

            <div class="form-group">
                <label for="content" class="form-label">Content</label>
                <textarea name="content" id="content" rows="6" class="form-control @error('content') is-invalid @enderror">{{ old('content', $article->content) }}</textarea>
                @error('content')
                    <p class="form-error">{{ $message }}</p>
                @enderror
            </div>

            <div class="form-actions">
                <button type="submit" class="btn-primary">Update</button>
                <a href="{{ route('articles.index') }}" class="btn-link">Cancel</a>
            </div>
        </form>
    </div>
@endsection
```

show

```php
@extends('layouts.app')

@section('content')
    <div class="detail-container">
        <div style="display: flex; justify-content: space-between; align-items: start;">
            <h1 class="detail-title">{{ $article->title }}</h1>
            <div style="display: flex; gap: 1rem;">
                @if($article->user_id === auth()->id())
                    <a href="{{ route('articles.edit', $article) }}" class="action-btn edit-btn" title="Edit">
                        <i class="fas fa-edit"></i>
                    </a>

                    <form method="POST" action="{{ route('articles.destroy', $article) }}" style="display:inline;" onsubmit="return confirm('Are you sure?')">
                        @csrf
                        @method('DELETE')
                        <button type="submit" class="action-btn delete-btn" title="Delete">
                            <i class="fas fa-trash-alt"></i>
                        </button>
                    </form>
                @endif
            </div>
        </div>

        <p class="detail-meta">
            Created by {{ $article->user->name }} on {{ $article->created_at->format('d.m.Y H:i') }}
        </p>

        <div class="detail-content">
            {{ $article->content }}
        </div>

        <a href="{{ route('dashboard') }}" class="back-link"><i class="fa-solid fa-arrow-left"></i> Back to list</a>
    </div>
@endsection
```

css

```html
<style>
        /* ===== BASE STYLES (from your existing app layout) ===== */
        body {
            margin: 0;
            font-family: system-ui, -apple-system, sans-serif;
            background-color: #f3f4f6;
            color: #1f2937;
        }

        .navbar {
            background-color: #ffffff;
            border-bottom: 1px solid #e5e7eb;
        }

        .navbar-container {
            max-width: 1280px;
            margin: 0 auto;
            padding: 0 16px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            height: 64px;
        }

        .navbar-logo {
            flex-shrink: 0;
            display: flex;
            align-items: center;
            font-size: 24px;
        }

        .nav-links {
            display: flex;
            margin-left: 40px;
            gap: 32px;
        }

        .nav-link {
            display: inline-flex;
            align-items: center;
            padding: 8px 12px;
            font-size: 15px;
            font-weight: 500;
            color: #4b5563;
            text-decoration: none;
            border-radius: 6px;
            transition: color 0.15s;
            border: none;
        }

        .nav-link:hover {
            color: #111827;
        }

        .user-dropdown {
            display: flex;
            align-items: center;
            margin-left: 24px;
        }

        .dropdown-trigger {
            display: inline-flex;
            align-items: center;
            padding: 8px 16px;
            border: 1px solid transparent;
            background: transparent;
            font-size: 14px;
            font-weight: 500;
            color: #6b7280;
            border-radius: 6px;
            cursor: pointer;
            transition: color 0.15s;
        }

        .dropdown-trigger:hover {
            color: #374151;
        }

        .dropdown-icon {
            width: 16px;
            height: 16px;
            margin-left: 8px;
            fill: currentColor;
        }

        .dropdown-content {
            background-color: #ffffff !important;
        }

        .main-content {
            max-width: 1280px;
            margin: 2rem auto;
            padding: 0 16px;
        }

        .big-title {
            font-size: 32px;
            font-weight: 700;
            margin-bottom: 10px;
        }

        .card-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
            gap: 1.5rem;
            margin-top: 10px;
        }

        .card {
            position: relative;
            background: white;
            border: 1px solid #e5e7eb;
            height: 200px;
            border-radius: 18px;
            padding: 1.25rem;
            padding-bottom: 60px;
            transition: box-shadow 0.25s ease, transform 0.25s ease;
        }

        .card-actions {
            position: absolute;
            bottom: 15px;
            right: 15px;
            display: flex;
            gap: 10px;
            align-items: center;
        }

        .action-btn {
            display: inline-flex;
            align-items: center;
            justify-content: center;
            width: 36px;
            height: 36px;
            border-radius: 50%;
            border: none;
            background-color: #f3f4f6;
            color: #1f2937;
            text-decoration: none;
            font-size: 16px;
            cursor: pointer;
            transition: all 0.2s ease;
            box-shadow: 0 2px 5px rgba(0,0,0,0.04);
        }

        .action-btn:hover {
            transform: scale(1.07);
        }

        .delete-btn:hover {
            color: #f1337c;
        }

        .card:hover {
            cursor: pointer;
            box-shadow: 0 8px 16px rgba(0, 0, 0, 0.04);
            transform: translateY(-3px);
        }

        .card-title {
            font-size: 1.25rem;
            font-weight: 600;
            margin-top: 0;
            margin-bottom: 0.5rem;
        }

        .card-content {
            color: #4b5563;
            margin-bottom: 1rem;
            line-height: 1.5;
        }

        .articles-table {
            width: 100%;
            border-collapse: collapse;
            background: white;
            border-radius: 18px;
            overflow: hidden;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.02);
        }

        .articles-table th {
            text-align: left;
            padding: 1rem 1.25rem;
            background-color: #f9fafb;
            font-weight: 600;
            color: #374151;
            border-bottom: 1px solid #e5e7eb;
        }

        .articles-table th:last-child {
            text-align: center;
        }

        .articles-table td {
            padding: 1rem 1.25rem;
            border-bottom: 1px solid #e5e7eb;
            color: #1f2937;
        }

        .articles-table tbody tr:last-child td {
            border-bottom: none;
        }

        .articles-table tbody {
            background-color: #ffffff;
        }

        .actions-cell {
            display: flex;
            gap: 8px;
            align-items: center;
            justify-content: center;
        }

        .actions-cell .action-btn {
            margin: 0;
        }

        /* ===== FORM STYLES (shared with guest pages) ===== */
        .form-container {
            background: white;
            border: 1px solid #e5e7eb;
            border-radius: 18px;
            padding: 2rem;
            max-width: 600px;
            margin: 0 auto;
        }

        .form-group {
            margin-bottom: 1.5rem;
        }

        .form-label {
            display: block;
            font-weight: 500;
            margin-bottom: 0.5rem;
            color: #374151;
        }

        .form-control {
            width: 100%;
            padding: 0.5rem 0.75rem;
            border: 1px solid #d1d5db;
            border-radius: 6px;
            font-size: 1rem;
            box-sizing: border-box;
        }

        .form-control:focus {
            outline: none;
            border-color: #ec1668;
            box-shadow: 0 0 0 3px rgba(236, 22, 104, 0.1);
        }

        textarea.form-control {
            min-height: 120px;
            resize: vertical;
        }

        .form-error {
            color: #dc2626;
            font-size: 0.875rem;
            margin-top: 0.25rem;
        }

        .form-actions {
            display: flex;
            gap: 1rem;
            align-items: center;
            margin-top: 2rem;
            justify-content: flex-end;
        }

        .btn-primary {
            background-color: #f1337c;
            color: white;
            border: none;
            padding: 10px 24px;
            border-radius: 24px;
            font-size: 1rem;
            font-weight: 500;
            cursor: pointer;
            transition: all 0.2s;
        }

        .btn-primary:hover {
            background-color: #ec1668;
            transform: scale(1.05);
        }

        .btn-link {
            color: #4b5563;
            text-decoration: none;
        }

        .btn-link:hover {
            text-decoration: underline;
        }

        .alert-success {
            background-color: #e2ffeb;
            border: 1px solid #8de39e;
            color: #065f3e;
            padding: 0.75rem 1rem;
            border-radius: 12px;
            margin-bottom: 1.5rem;
        }

        .detail-container {
            background: white;
            border: 1px solid #e5e7eb;
            border-radius: 24px;
            padding: 2rem;
        }

        .detail-title {
            font-size: 2rem;
            font-weight: 700;
            margin-top: 0;
            margin-bottom: 0.5rem;
        }

        .detail-meta {
            color: #6b7280;
            font-size: 0.875rem;
            margin-bottom: 1.5rem;
        }

        .detail-content {
            line-height: 1.8;
            font-size: 1.125rem;
            margin-bottom: 2rem;
        }

        .back-link {
            display: inline-block;
            margin-top: 10px;
            color: #ec1668;
            text-decoration: none;
            transition: all 0.2s ease;
        }

        .back-link:hover {
            scale: 1.05;
        }

        .hidden {
            display: none !important;
        }

        .profile-page {
            max-width: 600px;
            margin: auto;
        }

        .profile-title {
            font-size: 28px;
            font-weight: 700;
            margin-bottom: 25px;
        }

        .delete-container {
            border-color: #fecaca;
        }

        .btn-delete {
            background: #ef4444;
            color: white;
            border: none;
            padding: 10px 22px;
            border-radius: 24px;
            cursor: pointer;
            transition: all .2s;
        }

        .btn-delete:hover {
            background: #dc2626;
            transform: scale(1.05);
        }

        .modal {
            position: fixed;
            inset: 0;
            background: rgba(0,0,0,0.5);
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .modal-box {
            background: white;
            padding: 30px;
            border-radius: 16px;
            max-width: 420px;
            width: 100%;
        }

        .modal-actions {
            display: flex;
            justify-content: flex-end;
            gap: 10px;
            margin-top: 20px;
        }

        /* ===== NEW STYLES FOR GUEST PAGES ===== */
        .guest-layout {
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            background-color: #f3f4f6;
            padding: 1rem;
        }

        .guest-card {
            background: white;
            border-radius: 24px;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.05);
            width: 100%;
            max-width: 450px;
            padding: 2.5rem 2rem;
        }

        .guest-title {
            font-size: 2rem;
            font-weight: 700;
            color: #1f2937;
            margin-bottom: 0.5rem;
            text-align: center;
        }

        .guest-subtitle {
            color: #6b7280;
            text-align: center;
            margin-bottom: 2rem;
        }

        .guest-link {
            color: #f1337c;
            text-decoration: none;
            font-weight: 500;
        }

        .guest-link:hover {
            text-decoration: underline;
        }

        .checkbox-label {
            display: flex;
            align-items: center;
            gap: 0.5rem;
            color: #4b5563;
            font-size: 0.95rem;
            cursor: pointer;
        }

        .checkbox-label input[type="checkbox"] {
            width: 1rem;
            height: 1rem;
            accent-color: #f1337c;
        }
    </style>
```
